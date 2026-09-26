```py
!pip install --upgrade pymupdf
import pymupdf as fitz
from pathlib import Path

PDF_FILE = "1.pdf"
OUTPUT_MD = "kitap.md"
IMG_DIR = Path("img")
IMG_DIR.mkdir(exist_ok=True)

ZOOM = 2.0
MIN_FIG_AREA = 800
MERGE_GAP = 8
MAX_PAGE_AREA_RATIO = 0.70
ADD_PAGE_SEPARATOR = False  # True yaparsanız sayfalar arasına --- koyar

def area(r: fitz.Rect) -> float:
    return max(0, r.width) * max(0, r.height)

def expand(r: fitz.Rect, g: float) -> fitz.Rect:
    return fitz.Rect(r.x0 - g, r.y0 - g, r.x1 + g, r.y1 + g)

def close(a: fitz.Rect, b: fitz.Rect, gap: float) -> bool:
    return not (expand(a, gap) & b).is_empty

def merge_regions(rects, gap: float):
    rects = [fitz.Rect(r) for r in rects]
    changed = True
    while changed:
        changed = False
        i = 0
        while i < len(rects):
            j = i + 1
            while j < len(rects):
                if close(rects[i], rects[j], gap):
                    rects[i] = rects[i] | rects[j]
                    rects.pop(j)
                    changed = True
                else:
                    j += 1
            i += 1
    return rects

def block_text(block):
    parts = []
    for line in block.get("lines", []):
        for span in line.get("spans", []):
            parts.append(span.get("text", ""))
        parts.append("\n")
    return "".join(parts).strip()

def get_drawing_regions(page: fitz.Page):
    page_area = area(page.rect)
    rects = []
    for d in page.get_drawings():
        r = d.get("rect")
        if not r:
            continue

        a = area(r)
        if a < MIN_FIG_AREA:
            continue
        if page_area > 0 and (a / page_area) > MAX_PAGE_AREA_RATIO:
            continue

        w, h = r.width, r.height
        if w < 10 or h < 10:
            continue
        aspect = max(w, h) / max(1, min(w, h))
        if aspect > 20:
            continue

        rects.append(r)

    return merge_regions(rects, MERGE_GAP)

def page_to_markdown(page: fitz.Page, page_no: int):
    d = page.get_text("dict")
    blocks = d.get("blocks", [])

    items = []
    image_rects = []

    for b in blocks:
        btype = b.get("type", -1)
        r = fitz.Rect(b["bbox"])

        if btype == 0:
            txt = block_text(b)
            if txt:
                items.append(("text", r, txt))
        elif btype == 1:
            if area(r) >= MIN_FIG_AREA:
                items.append(("fig", r, "resim"))
                image_rects.append(r)

    for dr in get_drawing_regions(page):
        # Raster resimle büyük ölçüde çakışıyorsa tekrar say
        if any(
            not (dr & ir).is_empty and area(dr & ir) / max(1, area(dr)) > 0.5
            for ir in image_rects
        ):
            continue
        items.append(("fig", dr, "cizim"))

    items.sort(key=lambda x: (x[1].y0, x[1].x0))

    md = []
    fig_i = 0
    matrix = fitz.Matrix(ZOOM, ZOOM)

    for kind, rect, payload in items:
        if kind == "text":
            md.append(payload)
        else:
            fig_i += 1
            pix = page.get_pixmap(
                matrix=matrix,
                clip=rect,
                colorspace=fitz.csRGB,
                alpha=False
            )
            name = f"p{page_no:04d}_fig{fig_i:02d}_{payload}.png"
            pix.save(str(IMG_DIR / name))
            md.append(f"![{payload}]({IMG_DIR.name}/{name})")

    return "\n\n".join(md).strip()

all_pages = []
with fitz.open(PDF_FILE) as doc:
    for pno, page in enumerate(doc, start=1):
        all_pages.append(page_to_markdown(page, pno))

sep = "\n\n---\n\n" if ADD_PAGE_SEPARATOR else "\n\n"
Path(OUTPUT_MD).write_text(sep.join([p for p in all_pages if p]), encoding="utf-8")
```