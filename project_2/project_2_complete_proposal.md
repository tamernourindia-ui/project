# پروپوزال کامل پروژه دوم: سیستم تولید PowerPoint از PDF

## بخش I: معلومات کلی پروژه

### I.۱ نام پروژه
**"Intelligent PDF to Presentation System - سیستم هوشمند تولید پاورپوینت از مقاله PDF"**

### I.۲ هدف کلی
ایجاد یک **وب اپلیکیشن پیشرفته** که:
- فایل **PDF (مقاله علمی)** را از کاربر دریافت کند
- **نام کتاب/مقاله** را از کاربر بپرسد
- هوش مصنوعی منبع را **جستجو و تحقیق** کند
- متن مقاله را **خلاصه** کند (براساس **سطح خلاصه‌سازی** و **تعداد اسلایدهای** انتخاب‌شدهٔ کاربر)
- متن خلاصه‌شده را **ترجمهٔ تخصصی** کند (کلمه‌به‌کلمه، جمله‌به‌جمله، پاراگراف‌به‌پاراگراف)
- **دو نسخهٔ PowerPoint** تولید کند:
  - **نسخهٔ ارائه:** طراحی شیک، برای دانشجویان/استاد
  - **نسخهٔ ارائه‌دهنده:** ساده‌تر، با توضیحات کامل برای خود ارائه‌دهنده
- فونت‌های فارسی و انگلیسی را براساس **انتخاب کاربر** اعمال کند
- رنگ‌های **مناسب برای پروژکتور کم‌نور** استفاده کند
- انیمیشن‌ها برای transitions اسلایدها اضافه کند

### I.۳ پیش‌فرض اولیه
- **حوزهٔ پیش‌فرض:** چشم‌پزشکی (Ophthalmology)
- **اما:** سیستم خودکار حوزهٔ PDF را تشخیص دهد و شخصیت ترجمه‌کنندهٔ AI را تغییر دهد

---

## بخش II: تفاوت‌های کلیدی پروژه دوم با پروژه اول

| ویژگی | پروژه اول | پروژه دوم |
|------|----------|----------|
| **Input** | PowerPoint آماده | PDF (مقاله خام) |
| **خلاصه‌سازی** | نه | بله (هوشمند) |
| **Output** | یک فایل | دو فایل (نسخه‌های متفاوت) |
| **Presentation Design** | نه | بله (شیک برای نسخه ۱) |
| **Speaker Notes** | نه | بله (برای نسخه ۲) |
| **Customization** | نه | بله (فونت، رنگ، تعداد اسلاید) |
| **Animations** | نه | بله |
| **Theme Colors** | نه | بله (مناسب پروژکتور) |
| **پیچیدگی** | متوسط | بالا جداً |
| **مدت توسعه** | ۱۹-۲۸ روز | ۳۵-۴۵ روز |

---

## بخش III: معماری سیستم

### III.۱ معماری کلی

```
┌────────────────────────────────────────────────────────────┐
│                  User Interface                            │
│              (Next.js + React + TypeScript)               │
├────────────────────────────────────────────────────────────┤
│  Input:                                                    │
│  • نام مقاله                                              │
│  • فایل PDF                                               │
│  • تعداد اسلایدها (5-100)                                 │
│  • سطح خلاصه‌سازی (کم/متوسط/زیاد)                         │
│  • فونت فارسی (Vazir/Yekan/IRANSans/Samim)               │
│  • فونت انگلیسی (Calibri/Arial/Roboto)                   │
│  • سایز فونت (12-28)                                      │
│  • تمٔ رنگی (High Contrast/Dark/Professional)            │
└────────┬─────────────────────────────────────────────────┘
         │ HTTP Request (Multipart Form)
         ↓
┌────────────────────────────────────────────────────────────┐
│              Backend Server                                │
│         (Python + FastAPI + Async)                         │
├────────────────────────────────────────────────────────────┤
│ ┌──────────────────────────────────────────────────────┐  │
│ │ 1. Research Module                                   │  │
│ │    → جستجوی منبع (Gemini)                           │  │
│ │    → تشخیص حوزهٔ علمی                              │  │
│ │    → ایجاد واژه‌نامهٔ تخصصی                         │  │
│ └──────────────────────────────────────────────────────┘  │
│                       ↓                                     │
│ ┌──────────────────────────────────────────────────────┐  │
│ │ 2. PDF Processing Module                            │  │
│ │    → استخراج متن از PDF (pdfplumber)                │  │
│ │    → شناسایی ساختار (عنوان، بخش‌ها، پاراگراف‌ها)    │  │
│ │    → تقسیم‌بندی محتوا                               │  │
│ └──────────────────────────────────────────────────────┘  │
│                       ↓                                     │
│ ┌──────────────────────────────────────────────────────┐  │
│ │ 3. Summarization Module                             │  │
│ │    → خلاصه‌سازی هوشمند (Gemini)                      │  │
│ │    → تقسیم براساس تعداد اسلایدها                     │  │
│ │    → سطح خلاصه‌سازی                                  │  │
│ └──────────────────────────────────────────────────────┘  │
│                       ↓                                     │
│ ┌──────────────────────────────────────────────────────┐  │
│ │ 4. Translation Module                               │  │
│ │    → ترجمهٔ تخصصی هر بخش                            │  │
│ │    → استفاده از System Instructions                 │  │
│ │    → تغییر شخصیت خودکار                            │  │
│ └──────────────────────────────────────────────────────┘  │
│                       ↓                                     │
│ ┌──────────────────────────────────────────────────────┐  │
│ │ 5. Presentation Generation Module                   │  │
│ │    → تولید نسخهٔ ارائه (Presentation)                │  │
│ │    → تولید نسخهٔ ارائه‌دهنده (Presenter)             │  │
│ │    → اعمال فونت‌ها و رنگ‌ها                         │  │
│ │    → اضافهٔ انیمیشن‌ها و transitions                │  │
│ └──────────────────────────────────────────────────────┘  │
│                       ↓                                     │
│ ┌──────────────────────────────────────────────────────┐  │
│ │ 6. File Management Module                           │  │
│ │    → ذخیرهٔ دو فایل PPTX                            │  │
│ │    → تولید URLs دانلود                              │  │
│ └──────────────────────────────────────────────────────┘  │
└────────┬────────────────────────────────────────────────────┘
         │ Response + Two Files
         ↓
┌────────────────────────────────────────────────────────────┐
│              User Interface                                │
│  → نمایش پیشرفت و آمار                                     │
│  → دانلود نسخهٔ ارائه                                      │
│  → دانلود نسخهٔ ارائه‌دهنده                                │
└────────────────────────────────────────────────────────────┘
```

### III.۲ فناوری و ابزار

| بخش | تکنولوژی | ورژن | دلیل |
|-----|---------|------|------|
| **Frontend** | Next.js | 15.x | SSR، بهتری |
| | React | 18.x | تعاملی |
| | TypeScript | 5.x | Type safety |
| | Tailwind CSS | 3.x | Styling |
| | Shadcn/ui | Latest | UI Components |
| **Backend** | Python | 3.11+ | AI/NLP |
| | FastAPI | 0.100+ | Async |
| | python-pptx | 0.6+ | PPTX Generation |
| | pdfplumber | 0.10+ | PDF Processing |
| | google-generativeai | 0.3+ | Gemini API |
| **AI/ML** | Gemini 2.5 Pro | Latest | Summarization + Translation |
| **Database** | PostgreSQL | 15+ | History |
| **Storage** | S3 / Local | N/A | Files |
| **Queue** | Celery / RQ | Latest | Background Tasks |

---

## بخش IV: ورودی‌های کاربر (Input)

### IV.۱ فرمٔ ورودی مرحله‌به‌مرحله

```
مرحلهٔ ۱: اطلاعات مقاله
┌────────────────────────────────────────┐
│ نام دقیق مقاله:                       │
│ [_____________________________]        │
│ مثال: Diabetic Retinopathy Management │
└────────────────────────────────────────┘

مرحلهٔ ۲: آپلود PDF
┌────────────────────────────────────────┐
│ فایل PDF:                              │
│ [کشیدن و رها کردن]                    │
│ (حداکثر 100 MB)                      │
└────────────────────────────────────────┘

مرحلهٔ ۳: تنظیمات خلاصه‌سازی
┌────────────────────────────────────────┐
│ تعداد اسلایدهای دلخواه:                 │
│ [5 ────●─────── 100]                  │
│ انتخاب: 25 اسلاید                     │
│                                        │
│ سطح خلاصه‌سازی:                        │
│ ○ کم (خلاصهٔ بسیار مختصر)             │
│ ◉ متوسط (متعادل)                     │
│ ○ زیاد (جزئیات بیشتر)                │
└────────────────────────────────────────┘

مرحلهٔ ۴: تنظیمات فونت و رنگ
┌────────────────────────────────────────┐
│ فونت فارسی:                            │
│ [IRANSans ▼]                          │
│                                        │
│ فونت انگلیسی:                          │
│ [Calibri ▼]                           │
│                                        │
│ سایز فونت:                             │
│ [18 ▼]                                │
│                                        │
│ تمٔ رنگی:                              │
│ ○ High Contrast (سفید/مشکی)           │
│ ◉ Professional (آبی/سفید)             │
│ ○ Dark (تیره/روشن)                   │
└────────────────────────────────────────┘

         [شروع تولید پاورپوینت]
```

### IV.۲ Data Validation

```python
class PresentationRequest(BaseModel):
    paper_name: str = Field(..., min_length=3, max_length=500)
    num_slides: int = Field(..., ge=5, le=100)
    summarization_level: Literal["low", "medium", "high"]
    farsi_font: Literal["Vazir", "Yekan", "IRANSans", "Samim"]
    english_font: Literal["Calibri", "Arial", "Roboto"]
    font_size: int = Field(..., ge=12, le=28)
    color_theme: Literal["high_contrast", "professional", "dark"]
    file: UploadFile

@app.post("/api/v1/generate-presentation")
async def generate_presentation(request: PresentationRequest):
    # Validation انجام می‌شود خودکار
    pass
```

---

## بخش V: فرآیند دقیق کاری سیستم

### V.۱ مرحلهٔ ۱: جستجو و تحقیق

**همانند پروژه اول:**
```python
research_data = await research_source(paper_name)
# خروجی:
# {
#   "exact_title": "...",
#   "primary_field": "Ophthalmology",
#   "scientific_terms": {...},
#   "context_summary": "..."
# }
```

---

### V.۲ مرحلهٔ ۲: استخراج PDF و پردازش

```python
async def extract_pdf_content(file_path: str) -> dict:
    """
    استخراج متن و ساختار از PDF
    """
    import pdfplumber
    
    extracted_data = {
        "total_pages": 0,
        "content_sections": [],
        "raw_text": ""
    }
    
    with pdfplumber.open(file_path) as pdf:
        extracted_data["total_pages"] = len(pdf.pages)
        
        for page_num, page in enumerate(pdf.pages):
            # استخراج متن
            text = page.extract_text()
            
            # شناسایی ساختار (عناوین، بخش‌ها)
            section = {
                "page": page_num + 1,
                "text": text,
                "type": detect_section_type(text)  # title/heading/body
            }
            
            extracted_data["content_sections"].append(section)
            extracted_data["raw_text"] += text + "\n\n"
    
    return extracted_data

def detect_section_type(text: str) -> str:
    """
    شناسایی نوع بخش (عنوان، subtitle، متن معمولی)
    """
    if len(text) < 100:
        return "heading"
    elif text.isupper():
        return "title"
    else:
        return "body"
```

---

### V.۳ مرحلهٔ ۳: خلاصه‌سازی هوشمند

#### V.۳.۱ الگوریتم خلاصه‌سازی

```python
async def intelligent_summarization(
    raw_text: str,
    num_slides: int,
    summarization_level: str,
    context_summary: str
) -> list:
    """
    خلاصه‌سازی متن و تقسیم‌بندی براساس اسلایدها
    
    Returns:
        لیستی از slide contents
    """
    
    # تعیین نسبتٔ خلاصه‌سازی
    summary_ratios = {
        "low": 0.3,      # 30% از متن اصلی
        "medium": 0.5,   # 50% از متن اصلی
        "high": 0.7      # 70% از متن اصلی
    }
    
    ratio = summary_ratios[summarization_level]
    
    # Prompt برای Gemini
    summarization_prompt = f"""
تو یک استاد و متخصص در حوزهٔ "{context_summary}" هستی.

این مقاله را خلاصه کن و تقسیم‌بندی کن به {num_slides} بخش رایگاه:

متن مقاله:
"{raw_text}"

برای خلاصه‌سازی:
- نسبتٔ خلاصه‌سازی: {int(ratio*100)}% (سطح: {summarization_level})
- دقت علمی را حفظ کن
- از الفاظ اساسی استفاده کن
- ساختار منطقی را برقرار نگه‌دار

فرمت خروجی (JSON):
{{
    "slide_summaries": [
        {{
            "slide_number": 1,
            "title": "عنوان اسلاید",
            "content": "محتوای خلاصه‌شده",
            "key_points": ["نکتهٔ ۱", "نکتهٔ ۲"],
            "original_section": "بخش مرتبط از متن اصلی"
        }},
        ...
    ]
}}
"""
    
    client = genai.Client()
    response = client.models.generate_content(contents=summarization_prompt)
    
    slide_summaries = json.loads(response.text)
    return slide_summaries["slide_summaries"]
```

#### V.۳.۲ مثال خروجی خلاصه‌سازی

```json
{
    "slide_summaries": [
        {
            "slide_number": 1,
            "title": "مقدمه: رتینوپاتی دیابتی",
            "content": "رتینوپاتی دیابتی یکی از مهم‌ترین علل نابینایی در افراد فعال است. این بیماری به دلیل تأثیر طولانی‌مدت دیابت بر رگ‌های خون شبکیه ایجاد می‌شود.",
            "key_points": [
                "یکی از علل اصلی نابینایی",
                "ناشی از تأثیر دیابت بر شبکیه",
                "درمان‌پذیر اگر زود شناسایی شود"
            ],
            "original_section": "مقدمه - صفحاتٔ 1-3"
        },
        {
            "slide_number": 2,
            "title": "پاتوفیزیولوژی و آلیاژها",
            "content": "هیپرگلاسمی منجر به افزایش فشار اسمزی و صدمهٔ اندوتلیومی می‌شود. این فرآیند باعث افزایش نفوذپذیری و التهاب رگ‌های خون شبکیه می‌شود.",
            "key_points": [
                "هیپرگلاسمی → صدمهٔ اندوتلیومی",
                "افزایش نفوذپذیری رگ‌ها",
                "پاسخ التهابی"
            ],
            "original_section": "پاتوفیزیولوژی - صفحاتٔ 4-7"
        }
    ]
}
```

---

### V.۴ مرحلهٔ ۴: ترجمهٔ تخصصی

**همانند پروژه اول، اما برای هر slide:**

```python
async def translate_slide_content(
    slide_data: dict,
    system_instruction: str
) -> dict:
    """
    ترجمهٔ تمام اجزاء یک اسلاید
    """
    client = genai.Client()
    
    # ترجمهٔ عنوان
    title_translation = await translate_text(
        slide_data["title"],
        system_instruction
    )
    
    # ترجمهٔ محتوا
    content_translation = await translate_text(
        slide_data["content"],
        system_instruction
    )
    
    # ترجمهٔ نکات کلیدی
    key_points_translation = [
        await translate_text(point, system_instruction)
        for point in slide_data["key_points"]
    ]
    
    return {
        "title_fa": title_translation,
        "content_fa": content_translation,
        "key_points_fa": key_points_translation,
        "title_en": slide_data["title"],
        "content_en": slide_data["content"],
        "key_points_en": slide_data["key_points"]
    }
```

---

### V.۵ مرحلهٔ ۵: تولید دو نسخهٔ PowerPoint

#### V.۵.۱ نسخهٔ ۱: Presentation (برای ارائه)

```python
async def generate_presentation_version(
    slides_data: list,
    config: PresentationRequest
) -> str:
    """
    تولید نسخهٔ پیشرفتهٔ ارائه (طراحی شیک)
    """
    from pptx import Presentation
    from pptx.util import Inches, Pt
    from pptx.enum.text import PP_ALIGN
    from pptx.dml.color import RGBColor
    
    # ایجاد Presentation
    prs = Presentation()
    prs.slide_width = Inches(10)
    prs.slide_height = Inches(7.5)
    
    # تعیین رنگ‌های تمٔ
    theme_colors = get_theme_colors(config.color_theme)
    
    # اسلایدٔ عنوان
    title_slide = prs.slides.add_slide(prs.slide_layouts[6])  # Blank layout
    add_title_slide(title_slide, slides_data[0], config, theme_colors)
    
    # اسلایدهای محتوا
    for i, slide_data in enumerate(slides_data[1:], 1):
        slide = prs.slides.add_slide(prs.slide_layouts[6])
        
        # تنظیم پس‌زمینه
        background = slide.background
        fill = background.fill
        fill.solid()
        fill.fore_color.rgb = RGBColor(*theme_colors["background"])
        
        # اضافه کردن عنوان
        title_box = slide.shapes.add_textbox(
            Inches(0.5), Inches(0.3), Inches(9), Inches(0.8)
        )
        title_frame = title_box.text_frame
        title_frame.text = slide_data["title_fa"]
        title_frame.paragraphs[0].font.size = Pt(config.font_size + 4)
        title_frame.paragraphs[0].font.bold = True
        title_frame.paragraphs[0].font.color.rgb = RGBColor(*theme_colors["text_title"])
        title_frame.paragraphs[0].font.name = config.farsi_font
        
        # اضافه کردن محتوا
        content_box = slide.shapes.add_textbox(
            Inches(0.5), Inches(1.3), Inches(9), Inches(5.7)
        )
        content_frame = content_box.text_frame
        content_frame.word_wrap = True
        
        # محتوای اصلی
        p = content_frame.paragraphs[0]
        p.text = slide_data["content_fa"]
        p.font.size = Pt(config.font_size)
        p.font.name = config.farsi_font
        p.font.color.rgb = RGBColor(*theme_colors["text_body"])
        p.level = 0
        
        # نکات کلیدی
        for key_point in slide_data["key_points_fa"]:
            p = content_frame.add_paragraph()
            p.text = "• " + key_point
            p.font.size = Pt(config.font_size - 2)
            p.font.name = config.farsi_font
            p.font.color.rgb = RGBColor(*theme_colors["text_body"])
            p.level = 0
        
        # اضافه کردن انیمیشن
        add_animations(slide, config.color_theme)
    
    # ذخیرهٔ فایل
    output_file = f"presentation_{uuid.uuid4()}.pptx"
    prs.save(output_file)
    return output_file
```

#### V.۵.۲ نسخهٔ ۲: Presenter (برای ارائه‌دهنده)

```python
async def generate_presenter_version(
    slides_data: list,
    config: PresentationRequest
) -> str:
    """
    تولید نسخهٔ ساده‌تر برای ارائه‌دهنده (با Speaker Notes)
    """
    from pptx import Presentation
    from pptx.util import Inches, Pt
    
    prs = Presentation()
    prs.slide_width = Inches(10)
    prs.slide_height = Inches(7.5)
    
    # رنگ‌های ساده‌تر
    simple_colors = {
        "background": (255, 255, 255),  # سفید
        "text": (0, 0, 0)               # مشکی
    }
    
    for i, slide_data in enumerate(slides_data, 1):
        slide = prs.slides.add_slide(prs.slide_layouts[6])
        
        # اسلاید ساده (بدون طراحی)
        # عنوان
        title_box = slide.shapes.add_textbox(Inches(0.5), Inches(0.3), Inches(9), Inches(0.8))
        title_frame = title_box.text_frame
        title_frame.text = slide_data["title_fa"]
        title_frame.paragraphs[0].font.bold = True
        title_frame.paragraphs[0].font.size = Pt(config.font_size + 4)
        
        # محتوا
        content_box = slide.shapes.add_textbox(Inches(0.5), Inches(1.3), Inches(9), Inches(5.7))
        content_frame = content_box.text_frame
        content_frame.word_wrap = True
        p = content_frame.paragraphs[0]
        p.text = slide_data["content_fa"]
        
        # ⭐ Speaker Notes (نکات سخنگو)
        notes_slide = slide.notes_slide
        text_frame = notes_slide.notes_text_frame
        
        # بخش ۱: خلاصهٔ محتوا
        notes_text = f"""
📍 عنوان: {slide_data['title_fa']}

📝 محتوای اسلاید:
{slide_data['content_fa']}

🎯 نکات کلیدی:
{', '.join(slide_data['key_points_fa'])}

💡 نکات توضیحی برای سخنگو:
• این اسلاید درباره {slide_data['title_fa']} است
• مهم‌ترین نکات:
{chr(10).join('  - ' + point for point in slide_data['key_points_fa'])}

📚 متن اصلی (انگلیسی):
{slide_data['content_en']}

⏱️ زمان تقریبی: 2-3 دقیقه
        """
        
        text_frame.text = notes_text
    
    # ذخیرهٔ فایل
    output_file = f"presenter_{uuid.uuid4()}.pptx"
    prs.save(output_file)
    return output_file
```

#### V.۵.۳ تنظیمات رنگی مناسب برای پروژکتور

```python
def get_theme_colors(theme: str) -> dict:
    """
    دریافت پالتٔ رنگ براساس تمٔ انتخابی
    برای پروژکتورهای کم‌نور
    """
    themes = {
        "high_contrast": {
            "background": (255, 255, 255),      # سفید خالص
            "text_title": (0, 0, 0),            # مشکی خالص
            "text_body": (50, 50, 50),          # مشکی تیره
            "accent": (0, 51, 102),             # آبی تیره
            "highlight": (255, 153, 0)          # نارنجی
        },
        "professional": {
            "background": (240, 245, 250),      # آبی بسیار فاتح
            "text_title": (0, 51, 102),         # آبی تیره
            "text_body": (40, 40, 40),          # مشکی تیره
            "accent": (0, 102, 204),            # آبی
            "highlight": (255, 102, 0)          # نارنجی
        },
        "dark": {
            "background": (45, 45, 48),         # خاکستری تیره
            "text_title": (255, 255, 255),      # سفید
            "text_body": (220, 220, 220),       # خاکستری روشن
            "accent": (100, 200, 255),          # آبی روشن
            "highlight": (255, 200, 0)          # زرد
        }
    }
    return themes.get(theme, themes["professional"])
```

#### V.۵.۴ اضافه کردن انیمیشن‌ها

```python
def add_animations(slide, theme: str):
    """
    اضافه کردن انیمیشن برای transitions
    """
    # ⚠️ توجه: python-pptx به‌طور کامل از انیمیشن پشتیبانی نمی‌کند
    # راه‌حل: استفاده از template یا XML manipulation
    
    # روش ۱: استفاده از XML directly
    from pptx.oxml import parse_xml
    
    # Transition type
    transition_xml = """
    <p:transition xmlns:p="http://schemas.openxmlformats.org/presentationml/2006/main" 
                   spd="med" dur="800">
        <p:fade thruBlk="true"/>
    </p:transition>
    """
    
    # slide.element.insert(0, parse_xml(transition_xml))
    # یا استفاده از template قبلاً ساخته‌شده
```

---

### V.۶ تعیین و تغییر شخصیت

**استفاده از System Instructions از پروژه اول:**

```python
class PersonalityManager:
    """
    مدیریتٔ شخصیت براساس نوع مقاله
    (مثل پروژه اول، اما برای PDF)
    """
    
    @staticmethod
    def detect_field_from_pdf(research_data: dict) -> str:
        """تشخیص حوزهٔ PDF"""
        # کد مشابه پروژه اول
        pass
    
    @staticmethod
    def get_system_instruction(field: str) -> str:
        """دریافت System Instruction"""
        # کد مشابه پروژه اول
        pass
```

---

## بخش VI: رابط کاربری (UI/UX)

### VI.۱ صفحهٔ اصلی (Multi-Step Form)

```
┌──────────────────────────────────────────────────────┐
│  تولید پاورپوینت از مقاله PDF                        │
│  Intelligent PDF to Presentation Generator            │
├──────────────────────────────────────────────────────┤
│                                                      │
│  مرحلهٔ ۱ از ۴: اطلاعات مقاله                         │
│  ════════════════════════════════════════            │
│                                                      │
│  نام دقیق مقاله (انگلیسی):                           │
│  ┌──────────────────────────────────────────────┐   │
│  │ Diabetic Retinopathy: Diagnosis and Treatment   │
│  └──────────────────────────────────────────────┘   │
│                                                      │
│  آپلود فایل PDF:                                    │
│  ┌──────────────────────────────────────────────┐   │
│  │  [کشیدن و رها کردن یا انتخاب]                │   │
│  │  فایل: article.pdf (5.2 MB)                 │   │
│  └──────────────────────────────────────────────┘   │
│                                                      │
│    [الگو]  [بعدی]                                   │
└──────────────────────────────────────────────────────┘
```

### VI.۲ صفحهٔ تنظیمات (Step 2)

```
┌──────────────────────────────────────────────────────┐
│  مرحلهٔ ۲ از ۴: تنظیمات خلاصه‌سازی                    │
│  ════════════════════════════════════════            │
│                                                      │
│  تعداد اسلایدهای دلخواه: 25                         │
│  ┌──────────────────────────────────────┐           │
│  │5            ●             100        │           │
│  └──────────────────────────────────────┘           │
│  حداقل: 5 اسلاید | حداکثر: 100 اسلاید              │
│                                                      │
│  سطح خلاصه‌سازی:                                      │
│  ☐ کم    (خلاصهٔ 30% از متن)                        │
│  ☑ متوسط (خلاصهٔ 50% از متن)                        │
│  ☐ زیاد   (خلاصهٔ 70% از متن)                        │
│                                                      │
│    [الگو]  [بعدی]                                   │
└──────────────────────────────────────────────────────┘
```

### VI.۳ صفحهٔ تنظیمات فونت و رنگ (Step 3)

```
┌──────────────────────────────────────────────────────┐
│  مرحلهٔ ۳ از ۴: فونت و رنگ                          │
│  ════════════════════════════════════════            │
│                                                      │
│  فونت فارسی:                                         │
│  ┌────────────────────────┐                         │
│  │ IRANSans ▼             │                         │
│  └────────────────────────┘                         │
│                                                      │
│  فونت انگلیسی:                                       │
│  ┌────────────────────────┐                         │
│  │ Calibri ▼              │                         │
│  └────────────────────────┘                         │
│                                                      │
│  سایز فونت:                                          │
│  ┌────────────────────────┐                         │
│  │ 18 پیکسل ▼             │                         │
│  └────────────────────────┘                         │
│                                                      │
│  تمٔ رنگی (مناسب برای پروژکتور):                    │
│  ☐ High Contrast (سفید/مشکی)                       │
│  ☑ Professional (آبی/سفید)                         │
│  ☐ Dark (تیره/روشن)                                │
│                                                      │
│    [الگو]  [شروع تولید]                            │
└──────────────────────────────────────────────────────┘
```

### VI.۴ صفحهٔ پردازش (Processing)

```
┌──────────────────────────────────────────────────────┐
│         درحال تولید پاورپوینت...                    │
├──────────────────────────────────────────────────────┤
│                                                      │
│ 🔍 مرحلهٔ ۱: جستجو و تحقیق                          │
│    Diabetic Retinopathy Treatment                   │
│    ✓ تکمیل (۲ ثانیه)                               │
│                                                      │
│ 📄 مرحلهٔ ۲: استخراج و پردازش PDF                   │
│    کل صفحات: 28                                    │
│    ✓ تکمیل (۳ ثانیه)                               │
│                                                      │
│ ✂️ مرحلهٔ ۳: خلاصه‌سازی هوشمند                      │
│    هدف: 25 اسلاید                                  │
│    سطح: متوسط (50%)                                │
│    [████████████░░░░░░] 60%                        │
│    زمان: 45 ثانیه                                  │
│                                                      │
│ 🧠 مرحلهٔ ۴: تعیین حوزهٔ تخصصی                      │
│    حوزه: Ophthalmology                             │
│    شخصیت: چشم‌پزشک متخصص                          │
│    ✓ تکمیل (۱ ثانیه)                               │
│                                                      │
│ 🔄 مرحلهٔ ۵: ترجمهٔ تخصصی                          │
│    [██████████░░░░░░░░] 50%                        │
│    13 / 25 اسلاید ترجمه‌شده                         │
│    مدت زمان: ۱ دقیقه ۱۵ ثانیه                     │
│                                                      │
│ 🎨 مرحلهٔ ۶: تولید نسخه‌های PowerPoint               │
│    • نسخهٔ ارائه (Presentation)                    │
│    • نسخهٔ ارائه‌دهنده (Presenter Notes)            │
│    [██████░░░░░░░░░░░░] 30%                        │
│                                                      │
│ 💾 مرحلهٔ ۷: ذخیرهٔ فایل‌ها                         │
│    [██████░░░░░░░░░░░░] 30%                        │
│                                                      │
│ مدت زمان کل تقریبی: 3-5 دقیقه                      │
│                                                      │
└──────────────────────────────────────────────────────┘
```

### VI.۵ صفحهٔ نتیجه (Result)

```
┌──────────────────────────────────────────────────────┐
│    ✓ پاورپوینت با موفقیت تولید شد!                  │
├──────────────────────────────────────────────────────┤
│                                                      │
│ 📊 آمار و اطلاعات:                                  │
│  • منبع: Diabetic Retinopathy Treatment            │
│  • حوزهٔ تخصصی: Ophthalmology                      │
│  • صفحاتٔ PDF: 28                                  │
│  • تعداد اسلایدها: 25                              │
│  • سطح خلاصه‌سازی: متوسط (50%)                     │
│  • مدت زمان کل: 3 دقیقه 45 ثانیه                  │
│  • کیفیتٔ ترجمه: دکترای تخصصی                      │
│                                                      │
│ 📄 جزئیات منبع:                                    │
│  • عنوان: Diabetic Retinopathy...                 │
│  • نویسندگان: Dr. Smith, Dr. Johnson              │
│  • سال: 2024                                      │
│  • حوزه اصلی: Ophthalmology                        │
│  • زیرشاخه‌ها: Retinal Disease, Treatment         │
│  • واژهٔ تخصصی: 45                               │
│                                                      │
│ 🎬 تنظیمات اعمال‌شده:                               │
│  • فونت فارسی: IRANSans                           │
│  • فونت انگلیسی: Calibri                          │
│  • سایز: 18                                       │
│  • تم رنگی: Professional (آبی/سفید)               │
│  • انیمیشن‌ها: Fade transition                    │
│                                                      │
│ ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━      │
│                                                      │
│ 📥 دانلود نسخه‌ها:                                  │
│                                                      │
│  [⬇️ دانلود نسخهٔ ارائه]                            │
│  Presentation_25slides_Diabetic...pptx (2.3 MB)   │
│                                                      │
│  [⬇️ دانلود نسخهٔ ارائه‌دهنده]                       │
│  Presenter_25slides_Diabetic...pptx (2.5 MB)      │
│                                                      │
│ ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━      │
│                                                      │
│  [📋 کپی آمار]  [🔄 ایجاد ارائهٔ جدید]             │
│                                                      │
└──────────────────────────────────────────────────────┘
```

---

## بخش VII: API Endpoints

```
POST /api/v2/generate-presentation
├─ Request:
│  ├─ paper_name: string
│  ├─ num_slides: integer (5-100)
│  ├─ summarization_level: "low" | "medium" | "high"
│  ├─ farsi_font: string
│  ├─ english_font: string
│  ├─ font_size: integer (12-28)
│  ├─ color_theme: "high_contrast" | "professional" | "dark"
│  └─ file: file (.pdf)
│
├─ Response (Streaming via WebSocket):
│  ├─ status: "processing"
│  ├─ step: "researching" | "extracting" | "summarizing" | "translating" | "generating"
│  ├─ progress: 0-100
│  └─ message: string
│
└─ Final Response:
   ├─ status: "completed"
   ├─ presentation_url: string
   ├─ presenter_url: string
   ├─ statistics: object
   └─ duration: integer

GET /api/v2/download/:file_id
└─ Response: Binary file (.pptx)

POST /api/v2/personality/detect
├─ Request:
│  └─ research_data: object
│
└─ Response:
   ├─ field: string
   ├─ personality: string
   └─ system_instruction: string
```

---

## بخش VIII: Database Schema

```sql
-- جدولٔ وظایف تولید ارائه
CREATE TABLE presentation_jobs (
    id UUID PRIMARY KEY,
    user_id UUID,
    paper_name VARCHAR(500),
    pdf_file_name VARCHAR(500),
    num_slides INTEGER,
    summarization_level VARCHAR(50),
    primary_field VARCHAR(100),
    presentation_file_url VARCHAR(1000),
    presenter_file_url VARCHAR(1000),
    status VARCHAR(50),
    created_at TIMESTAMP,
    completed_at TIMESTAMP,
    duration_seconds INTEGER,
    research_data JSONB,
    slides_data JSONB,
    statistics JSONB
);
```

---

## بخش IX: Timeline توسعه

| فاز | کار | مدت | تکمیل شدگی |
|-----|-----|-----|-----------|
| ۱ | Setup + PDF Processing | ۲-۳ روز | pdfplumber OK |
| ۲ | Summarization Module | ۳-۴ روز | Gemini API OK |
| ۳ | Translation Integration | ۲-۳ روز | Translate all slides |
| ۴ | Presentation Generation | ۴-۵ روز | Both versions OK |
| ۵ | UI/UX Frontend | ۴-۵ روز | Multi-step form |
| ۶ | Integration | ۲-۳ روز | Frontend + Backend |
| ۷ | Testing | ۳-۴ روز | All features |
| ۸ | Optimization | ۲-۳ روز | Performance |
| ۹ | Deployment | ۱-۲ روز | Live |
| | **مجموع** | **۲۶-۳۲ روز** | |

---

## بخش X: نکات اهم و دقیق

### X.۱ Summarization Quality Control

```python
# بررسی کیفیتٔ خلاصه‌سازی
def validate_summarization(
    original_text: str,
    summary: str,
    target_ratio: float
) -> bool:
    """
    اگر نسبتٔ خلاصه‌سازی درست نیست، دوباره امتحان کن
    """
    original_words = len(original_text.split())
    summary_words = len(summary.split())
    actual_ratio = summary_words / original_words
    
    # تحمل: ±10%
    tolerance = 0.1
    if abs(actual_ratio - target_ratio) > tolerance:
        return False
    
    return True
```

### X.۲ Speaker Notes Best Practices

```python
# متن Speaker Notes باید:
# ✅ خلاصهٔ متن اسلاید
# ✅ نکات توضیحی اضافی
# ✅ کلیدواژه‌های کلیدی
# ✅ ارجاعات به متن اصلی
# ✅ توصیات ارائهٔ زمان (۲-۳ دقیقه/اسلاید)
```

### X.۳ Animation و Transition

```python
# Transitions مناسب برای هر تم:
# High Contrast: Push (سریع)
# Professional: Fade (ملایم)
# Dark: Wipe (دینامیکی)
```

---

## بخش XI: تفاوت‌های کلیدی دو نسخهٔ PowerPoint

| ویژگی | نسخهٔ ارائه | نسخهٔ ارائه‌دهنده |
|------|-----------|----------------|
| **طراحی** | شیک و رنگین | ساده و متمرکز |
| **Animations** | کامل | بدون |
| **Fonts** | متنوع | استاندارد |
| **Colors** | حسین‌ها | محدود |
| **Speaker Notes** | نه | بله (کامل) |
| **Target Audience** | تمام مخاطبان | خود ارائه‌دهنده |
| **Focus** | Visual Appeal | Content Clarity |

---

## بخش XII: منابع و مستندات

- Gemini API: https://ai.google.dev
- python-pptx: https://python-pptx.readthedocs.io
- pdfplumber: https://github.com/jsvine/pdfplumber
- FastAPI: https://fastapi.tiangolo.com
- Next.js: https://nextjs.org

---

## ملاحظات نهایی

**پروژه دوم پیچیده‌تر است:**
- خلاصه‌سازی هوشمند
- تولید دو نسخهٔ متفاوت
- طراحی و انیمیشن
- Speaker Notes کامل
- مدت توسعه: ۲۶-۳۲ روز

**اما اساس (Codebase) از پروژه اول استفاده می‌شود:**
- System Instructions (재사용 می‌شود)
- Personality Manager (재사용 می‌شود)
- Translation Module (재사용 می‌شود)

موفق باشید! 🚀

