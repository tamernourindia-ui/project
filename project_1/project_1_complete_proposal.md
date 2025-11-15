# پروپوزال کامل پروژه اول: سیستم ترجمه تخصصی PowerPoint

## بخش I: معلومات کلی پروژه

### I.۱ نام پروژه
**"Smart Medical PowerPoint Translator - سیستم ترجمهٔ تخصصی PowerPoint"**

### I.۲ هدف کلی
ایجاد یک **وب اپلیکیشن هوشمند** که:
- فایل PowerPoint انگلیسی را از کاربر دریافت کند
- **نام کتاب/مقاله/منبع** را از کاربر بپرسد
- هوش مصنوعی (Gemini 2.5 Pro) این منبع را جستجو و تحقیق کند
- **متن‌های توضیحی و محتوای ساده** را به **فارسی تخصصی** ترجمه کند
- **عنوان‌ها، کپشن تصاویر، متن‌های داخل جداول و نمودارها ترجمه نشوند**
- کلمات تخصصی بدون معادل فارسی به انگلیسی باقی بمانند
- **تمام فرمت‌بندی، استایل، تصاویر، نمودارها** را **۱۰۰% دست‌نخورده** نگه‌دارد
- **فونت‌های فارسی مناسب** را خودکار اعمال کند
- **تمام متن‌ها راست‌چین (RTL)** شوند
- **گزارش PDF** از واژه‌های تخصصی تولید شود
- فایل نهایی را برای دانلود فراهم کند

### I.۳ پیش‌فرض اولیه
**حوزهٔ تخصصی پیش‌فرض:** چشم‌پزشکی (Ophthalmology)
**اما:** سیستم باید خودکار حوزهٔ محتوا را تشخیص دهد و شخصیت و دانش ترجمه‌کنندهٔ AI را تغییر دهد.
**ذخیره‌سازی:** فایل‌ها روی سرور (تا ۷ روز) نگه‌داری شده و سپس حذف شوند. دانلود روی سیستم کاربر.

---

## بخش II: معماری سیستم

### II.۱ معماری کلی

```
┌─────────────────────────────────────────────────────────┐
│                      User Interface                     │
│                    (Next.js + React)                    │
├─────────────────────────────────────────────────────────┤
│  Input: نام منبع + فایل PowerPoint                      │
└────────────────┬────────────────────────────────────────┘
                 │ HTTP Request
                 ↓
┌─────────────────────────────────────────────────────────┐
│                   Backend Server                        │
│              (Python + FastAPI)                         │
├─────────────────────────────────────────────────────────┤
│ ┌─────────────────────────────────────────────────────┐ │
│ │ 1. Research Module (Gemini API Search)             │ │
│ │    → تحقیق و شناسایی منبع                            │ │
│ │    → تشخیص حوزهٔ علمی                              │ │
│ │    → ایجاد واژه‌نامهٔ تخصصی                         │ │
│ └─────────────────────────────────────────────────────┘ │
│                       ↓                                   │
│ ┌─────────────────────────────────────────────────────┐ │
│ │ 2. Smart PPTX Processor Module                      │ │
│ │    → دیکته‌آوری هوشمند نوع متن (عنوان/محتوا)       │ │
│ │    → فیلتر کردن متن‌های غیرقابل ترجمه              │ │
│ │    → استخراج فقط متن‌های ترجمه‌پذیر                │ │
│ │    → نگه‌داری ساختار و فرمت اصلی                  │ │
│ │    → ذخیرهٔ shape objects                          │ │
│ └─────────────────────────────────────────────────────┘ │
│                       ↓                                   │
│ ┌─────────────────────────────────────────────────────┐ │
│ │ 3. Translation Module (Gemini 2.5 Pro)            │ │
│ │    → ترجمهٔ دقیق هر متن توضیحی                      │ │
│ │    → استفاده از System Instructions تخصصی         │ │
│ │    → کنترل تغییر شخصیت                            │ │
│ │    → محدود کردن به حوزهٔ تخصصی تشخیص‌شده          │ │
│ │    → حفظ کلمات تخصصی بدون معادل فارسی             │ │
│ └─────────────────────────────────────────────────────┘ │
│                       ↓                                   │
│ ┌─────────────────────────────────────────────────────┐ │
│ │ 4. Persian Formatting Module                       │ │
│ │    → اعمال فونت‌های فارسی خودکار                   │ │
│ │    → راست‌چین کردن تمام متن‌ها                     │ │
│ │    → حفظ ترازبندی‌های خاص                         │ │
│ └─────────────────────────────────────────────────────┘ │
│                       ↓                                   │
│ ┌─────────────────────────────────────────────────────┐ │
│ │ 5. PDF Report Generator                             │ │
│ │    → تولید گزارش واژه‌های تخصصی                     │ │
│ │    → لیست مترادف‌ها و اصطلاحات                     │ │
│ └─────────────────────────────────────────────────────┘ │
│                       ↓                                   │
│ ┌─────────────────────────────────────────────────────┐ │
│ │ 6. PPTX Writer Module                              │ │
│ │    → جایگزینی متن‌های اصلی با فارسی              │ │
│ │    → حفظ کامل فرمت و استایل                       │ │
│ │    → ذخیرهٔ فایل نهایی                             │ │
│ └─────────────────────────────────────────────────────┘ │
└────────────────┬────────────────────────────────────────┘
                 │ Response + Files
                 ↓
┌─────────────────────────────────────────────────────────┐
│                   User Interface                        │
│          → نمایش پیشرفت و آمار                          │
│          → دانلود فایل PPTX ترجمه‌شده                 │
│          → دانلود گزارش PDF واژه‌ها                   │
└─────────────────────────────────────────────┬──────────┘
                                              │
┌─────────────────────────────────────────────▼──────────┐
│                   Local Storage                          │
│          → فایل‌ها روی سیستم کاربر ذخیره شوند          │
└─────────────────────────────────────────────────────────┘
```

### II.۲ فناوری و ابزار

| بخش | تکنولوژی | ورژن | دلیل انتخاب |
|-----|---------|------|-----------|
| **Frontend** | Next.js | 15.x | SSR، بهتری بودن، تجربهٔ شما |
| | React | 18.x | کمپوننت‌های تعاملی |
| | TypeScript | 5.x | Type safety و development تسریع |
| | Tailwind CSS | 3.x | Styling سریع و responsive |
| | Axios | 1.6+ | HTTP client |
| | reportlab | 4.0+ | تولید PDF reports |
| **Backend** | Python | 3.11+ | بهتری برای AI و NLP |
| | FastAPI | 0.100+ | Async و سریع |
| | python-pptx | 0.6+ | خواندن/نوشتن PPTX |
| | reportlab | 4.0+ | تولید گزارش PDF |
| | google-generativeai | 0.3+ | Gemini API |
| **AI/ML** | Gemini 2.5 Pro | Latest | ترجمهٔ دقیق و تخصصی |
| **Storage** | Local Temp Storage | N/A | فایل‌ها روی سیستم کاربر |
| **Server Storage** | Temporary Files | N/A | حداکثر ۷ روز نگه‌داری |

---

## بخش III: فرآیند دقیق کاری سیستم

### III.۱ مرحلهٔ ۱: دریافت ورودی‌های کاربر

**Input:**
```
۱. نام کتاب/مقاله/منبع (String)
   مثال: "Deep Learning", "Convolutional Neural Networks in Medical Imaging"

۲. فایل PowerPoint (.pptx)
   فرمت: Multipart Form-Data
   حد اکثر اندازه: ۵۰ MB

۳. (اختیاری) حوزهٔ تخصصی (جدید)
   برای بهبود دقت تشخیص حوزه
```

**Validation:**
```python
def validate_inputs(book_name: str, file: UploadFile) -> bool:
    # بررسی نام منبع
    if not book_name or len(book_name) < 3:
        raise ValueError("نام منبع باید حداقل ۳ کاراکتر باشد")

    # بررسی فرمت فایل
    if not file.filename.endswith('.pptx'):
        raise ValueError("فقط فایل‌های .pptx قبول هستند")

    # بررسی اندازهٔ فایل
    if file.size > 50 * 1024 * 1024:  # ۵۰ MB
        raise ValueError("اندازهٔ فایل بیش از حد است")

    return True
```

### III.۲ مرحلهٔ ۲: هوشمندی در تشخیص نوع متن (Smart Text Detection)

#### III.۲.۱ الگوریتم تشخیص هوشمند

```python
class SmartTextDetector:
    """سیستم هوشمند تشخیص نوع متن برای ترجمه"""

    TITLE_KEYWORDS = [
        'title', 'heading', 'header', 'caption', 'figure', 'table',
        'chart', 'graph', 'diagram', 'slide title', 'main title'
    ]

    TRANSLATABLE_PATTERNS = [
        r'^[A-Z][^.!?]*$',  # جملات ابتدایی
        r'.*[.!?]$',        # جملات کامل
        r'\w+ \w+',         # کلمات چندگانه
        r'.{50,}',          # متن‌های بلند
    ]

    NON_TRANSLATABLE_PATTERNS = [
        r'^\d+\.?\s*$',     # اعداد
        r'^[A-Z]{2,10}$',   # اختصارات
        r'^\d+%',           # درصد
        r'^\(.*\)$',        # پرانتزهای کوتاه
        r'fig\.?\s*\d+',    # شماره شکل
        r'table\s*\d+',     # شماره جدول
    ]

    @staticmethod
    def should_translate(text: str, shape_type: str) -> bool:
        """
        تصمیم گیری هوشمند برای ترجمه متن
        """
        text = text.strip()

        # متن‌های کوتاه را ترجمه نکن
        if len(text) < 5:
            return False

        # متن‌های عددی یا اختصار را ترجمه نکن
        for pattern in SmartTextDetector.NON_TRANSLATABLE_PATTERNS:
            if re.match(pattern, text, re.IGNORECASE):
                return False

        # shape type عنوان دارد
        if shape_type.lower() in ['title', 'heading', 'header', 'caption']: # Added 'caption'
            return False

        # بررسی الگوهای ترجمه‌پذیر
        for pattern in SmartTextDetector.TRANSLATABLE_PATTERNS:
            if re.match(pattern, text):
                return True

        # پیش‌فرض: اگر توضیحی به نظر برسد
        return len(text.split()) > 3
```

#### III.۲.۲ مثال‌های تشخیص هوشمند

```python
# مثال‌های ترجمه نشوند:
"Retinal Detachment" → False (عنوان)
"Fig. 2: OCT Scan" → False (کپشن)
"Table 1" → False (شماره جدول)
"95%" → False (عدد)

# مثال‌های ترجمه شوند:
"This is a common cause of vision loss in elderly patients"
"Types of surgical interventions include scleral buckle procedure"
"The patient presented with symptoms of decreased visual acuity"
```

**پیشنهادات و بهبودها برای SmartTextDetector:**
1.  **استفاده از NLP پیشرفته:** برای افزایش دقت `SmartTextDetector`، از کتابخانه‌های NLP مانند NLTK یا spaCy برای تشخیص مرز جمله (Sentence Boundary Detection)، تحلیل گرامری و شناسایی موجودیت‌های نام‌گذاری شده (Named Entity Recognition - NER) استفاده شود. این کار به سیستم کمک می‌کند تا بین عنوان‌ها، فهرست‌ها و متن‌های توضیحی واقعی با دقت بیشتری تمایز قائل شود.
2.  **تشخیص نوع Shape دقیق‌تر:** به جای فقط بررسی `shape.slide.shapes.title` یا موقعیت فیزیکی برای کپشن، از `python-pptx` برای شناسایی `placeholder_format.type` (مانند `TITLE`, `BODY`, `CHART_TITLE`, `PICTURE_CAPTION`) استفاده شود که معمولاً دقیق‌تر هستند. ترکیب این روش‌ها با heuristics فعلی می‌تواند قدرت تشخیص را بالا ببرد.

---

### III.۳ مرحلهٔ ۳: پردازش هوشمند PPTX

```python
async def process_pptx_smart(file_path: str) -> dict:
    """
    پردازش هوشمند فایل PPTX با فیلتر متن‌ها
    """
    prs = Presentation(file_path)

    processed_data = {
        "total_slides": len(prs.slides),
        "translatable_texts": [],
        "non_translatable_texts": [],
        "metadata": {}
    }

    detector = SmartTextDetector()

    for slide_index, slide in enumerate(prs.slides):
        slide_data = {
            "slide_id": slide_index,
            "translatable": [],
            "non_translatable": []
        }

        for shape_index, shape in enumerate(slide.shapes):
            if hasattr(shape, "text_frame"):
                original_text = shape.text.strip()

                if original_text:
                    # تشخیص نوع shape
                    shape_type = get_shape_type_advanced(shape)

                    # تصمیم برای ترجمه
                    should_translate = detector.should_translate(
                        original_text, shape_type
                    )

                    text_info = {
                        "shape_id": shape_index,
                        "text": original_text,
                        "shape_type": shape_type,
                        "shape_ref": id(shape)
                    }

                    if should_translate:
                        slide_data["translatable"].append(text_info)
                    else:
                        slide_data["non_translatable"].append(text_info)

        processed_data["slides"].append(slide_data)

    return processed_data

def get_shape_type_advanced(shape) -> str:
    """تشخیص پیشرفته نوع shape"""
    # عنوان اصلی اسلاید
    if shape == shape.slide.shapes.title:
        return "slide_title"

    # چک کردن نام placeholder
    if hasattr(shape, 'placeholder_format'):
        ph_type = shape.placeholder_format.type
        if ph_type == 1:  # عنوان
            return "title"
        elif ph_type == 2:  # body/caption
            return "body"
        # اضافه کردن تشخیص برای انواع دیگر placeholder
        elif ph_type == 3: # Chart Title
            return "chart_title"
        elif ph_type == 4: # Picture Caption
            return "picture_caption"


    # بررسی اندازه و موقعیت برای تشخیص کپشن
    slide_width = shape.slide.slide_width
    slide_height = shape.slide.slide_height

    if (shape.top > slide_height * 0.8 or
        shape.left < slide_width * 0.1):
        return "caption"

    return "content"
```

**III.۳.۱ الگوریتم تشخیص حوزه تخصصی (Domain Detection Algorithm)**
برای ماژول `Research Module`، یک زیربخش جداگانه با عنوان "Domain Detection Algorithm" اضافه می‌شود. این الگوریتم می‌تواند شامل موارد زیر باشد:
*   استخراج کلمات کلیدی از `book_name` و اسلایدهای اولیه.
*   استفاده از Embeddings و Semantic Search با یک دیتابیس از مقالات تخصصی یا ویکیپدیا برای یافتن نزدیک‌ترین حوزه.
*   استفاده از Gemini برای طبقه‌بندی متن‌ها به حوزه‌های از پیش تعریف شده (مثلاً Ophthalmology, Neurology, Cardiology).
*   در نظر گرفتن `field_override` اختیاری از کاربر که می‌تواند به الگوریتم کمک کند یا آن را لغو کند.

---

### III.۴ مرحلهٔ ۴: ترجمهٔ تخصصی محدود به حوزه

#### III.۴.۱ تغییرات در Personality Manager

```python
class PersonalityManager:
    # ... (کدهای قبلی حفظ شوند)

    # تغییرات جدید:
    FIELDS = {
        'ophthalmology': {
            'specialized_only': True,  # محدود به حوزه
            'fallback_allowed': False, # اجازه fallback به عمومی ندارد
            'domains': ['retina', 'optic', 'eye', 'vision', 'cataract', 'glaucoma']
        },
        'neurology': {
            # مشابه برای دیگر حوزه‌ها
        }
    }

    @staticmethod
    def get_constrained_system_instruction(field: str) -> str:
        """
        System Instruction محدود به حوزه تخصصی
        """
        base_instruction = PersonalityManager.PERSONALITIES.get(field, {})['system_instruction']

        constraint = f"""
**قانون بسیار مهم:**
- فقط در حوزه {field} متخصص هستی
- اگر متن خارج از حوزه باشد، آن را ترجمه نکن
- کلمات تخصصی بی معادل فارسی را به انگلیسی بگذار
- مثال: "photoreceptor" (گیرنده نوری هم اشتباه است)

{base_instruction}
        """

        return constraint
```

---

### III.۵ مرحلهٔ ۵: اعمال فرمت فارسی (RTL + Font)

```python
class PersianFormatter:
    """سیستم فرمت‌دهی فارسی"""

    PERSIAN_FONTS = [
        'B Nazanin',      # پیش‌فرض و بهترین
        'IRANSans',       # مدرن و خوانا
        'Vazir',          # FOSS و زیبا
        'Yekan',          # گوگل فونت
        'Sahel',          # پسرعمو
        'Tahoma',         # ویندوزی
    ]

    @staticmethod
    def apply_persian_formatting(text_frame):
        """
        اعمال فرمت فارسی روی text frame
        """
        # اعمال راست‌چین روی پاراگراف‌ها
        for paragraph in text_frame.paragraphs:
            paragraph.alignment = PP_ALIGN.RTL  # راست‌چین کامل

            # اعمال فونت فارسی
            for run in paragraph.runs:
                run.font.name = PersianFormatter.select_best_font()

                # تنظیمات فونت
                run.font.rtl = True  # راست به چپ
                run.font.complex_script = True  # پشتیبانی از فارسی

    @staticmethod
    def select_best_font() -> str:
        """
        انتخاب هوشمند بهترین فونت فارسی
        """
        return PersianFormatter.PERSIAN_FONTS[0]  # B Nazanin
```

---

### III.۶ مرحلهٔ ۶: تولید گزارش PDF واژه‌ها

```python
from reportlab.pdfgen import canvas
from reportlab.lib.pagesizes import letter
from reportlab.lib.styles import getSampleStyleSheet
from reportlab.platypus import SimpleDocTemplate, Table, TableStyle
from reportlab.lib import colors
from reportlab.lib.fonts import addMapping
from reportlab.pdfbase import pdfmetrics
from reportlab.pdfbase.ttfonts import TTFont
from arabic_reshaper import ArabicReshaper
from bidi.algorithm import get_display

class TerminologyReportGenerator:
    """تولید گزارش PDF واژه‌های تخصصی"""

    def __init__(self):
        self.styles = getSampleStyleSheet()
        # تنظیم فونت فارسی برای reportlab
        try:
            pdfmetrics.registerFont(TTFont('BNazanin', '/path/to/BNazanin.ttf')) # مسیر واقعی فونت باید تنظیم شود
            self.font_name = 'BNazanin'
        except:
            self.font_name = 'Helvetica' # Fallback

        self.PersianStyle = ParagraphStyle(
            'Persian',
            parent=self.styles['Normal'],
            fontName=self.font_name,
            fontSize=12,
            alignment=2,  # راست‌چین
            leading=14,
        )
        self.reshaper = ArabicReshaper()


    def _format_persian_text(self, text: str) -> str:
        """فرمت‌دهی متن فارسی برای نمایش صحیح در PDF"""
        reshaped_text = self.reshaper.reshape(text)
        bidi_text = get_display(reshaped_text)
        return bidi_text

    async def generate_report(
        self,
        research_data: dict,
        translations: dict,
        output_path: str
    ) -> str:
        """
        تولید گزارش کامل واژه‌ها
        """
        doc = SimpleDocTemplate(output_path, pagesize=letter)
        story = []

        # عنوان گزارش
        title_style = self.styles['Heading1']
        story.append(Paragraph(self._format_persian_text("گزارش واژه‌ها و اصطلاحات تخصصی"), self.PersianStyle))

        # اطلاعات منبع
        story.append(Paragraph(self._format_persian_text(f"منبع: {research_data.get('exact_title', '')}"), self.PersianStyle))
        story.append(Paragraph(self._format_persian_text(f"حوزه: {research_data.get('primary_field', '')}"), self.PersianStyle))

        # جدول واژه‌ها
        terms_data = [[self._format_persian_text('واژه انگلیسی'), self._format_persian_text('ترجمه فارسی'), self._format_persian_text('حوزه کاربرد')]]

        scientific_terms = research_data.get('scientific_terms', {})
        for eng_term, info in scientific_terms.items():
            persian = self._format_persian_text(info.get('persian', '-'))
            terms_data.append([eng_term, persian, self._format_persian_text(research_data.get('primary_field', ''))])

        table = Table(terms_data)
        table.setStyle(TableStyle([
            ('BACKGROUND', (0, 0), (-1, 0), colors.grey),
            ('TEXTCOLOR', (0, 0), (-1, 0), colors.whitesmoke),
            ('ALIGN', (0, 0), (-1, -1), 'CENTER'),
            ('FONTNAME', (0, 0), (-1, 0), self.font_name if self.font_name != 'Helvetica' else 'Helvetica-Bold'),
            ('FONTSIZE', (0, 0), (-1, 0), 14),
            ('BOTTOMPADDING', (0, 0), (-1, 0), 12),
            ('BACKGROUND', (0, 1), (-1, -1), colors.beige),
            ('GRID', (0, 0), (-1, -1), 1, colors.black)
        ]))

        story.append(table)

        # آمار ترجمه
        stats = self.calculate_translation_stats(translations)
        stats_text = self._format_persian_text(f"""
        آمار ترجمه:
        - کل متن‌های ترجمه‌شده: {stats['translated_count']}
        - واژه‌های تخصصی شناسایی‌شده: {len(scientific_terms)}
        - حوزه تخصصی: {research_data.get('primary_field', '')}
        """)

        story.append(Paragraph(stats_text, self.PersianStyle))

        doc.build(story)
        return output_path

    def calculate_translation_stats(self, translations: dict) -> dict:
        """محاسبه آمار ترجمه"""
        total_translated = 0
        for slide_texts in translations.values():
            total_translated += len(slide_texts)

        return {
            'translated_count': total_translated,
            'unique_terms': len(set()),  # می‌توان گسترش داد
        }
```

**نکته مهم برای پشتیبانی از فونت‌های فارسی در `reportlab`:**
برای نمایش صحیح متن‌های فارسی در PDF، نیاز به نصب فونت‌های فارسی (مانند `BNazanin.ttf`) روی سرور و استفاده از ابزارهایی مانند `arabic_reshaper` برای شکل‌دهی صحیح حروف و `python-bidi` برای مدیریت راست‌چین بودن متن خواهید داشت. مسیر فونت در کد `pdfmetrics.registerFont` باید به درستی تنظیم شود. همچنین، اطمینان از اینکه فونت‌های مورد نیاز در محیط سرور در دسترس هستند، حیاتی است.

---

### III.۷ مرحلهٔ ۷: مدیریت خطا و پایداری (Error Handling & Robustness)

جزئیات مربوط به مدیریت خطاها و پایداری سیستم:
*   **خطاهای ورودی:** استفاده از `validate_inputs` برای بررسی فرمت و اندازه فایل و ارائه بازخورد مناسب به کاربر.
*   **خطاهای API Gemini:** پیاده‌سازی retry mechanisms با exponential backoff برای تماس‌های API به منظور افزایش پایداری. لاگ‌برداری دقیق از خطاها و ارائه پیام‌های خطای کاربرپسند.
*   **فایل‌های PPTX خراب:** افزودن `try-except` بلاک‌های قوی‌تر در `process_pptx_smart` و `update_pptx_with_translations` برای مدیریت فایل‌های آسیب‌دیده و جلوگیری از کرش سیستم.
*   **متن‌های غیرقابل پردازش:** ارائهٔ پیام‌های خطای واضح به کاربر در صورت بروز مشکل در پردازش متن یا شناسایی محتوای نامعتبر.
*   **لاگ‌برداری:** استفاده از سیستم لاگ‌برداری جامع (مانند `logging` در پایتون) برای ردیابی خطاها، هشدارها و اطلاعات مهم در طول فرآیند، که به اشکال‌زدایی و مانیتورینگ کمک می‌کند.

### III.۸ مرحلهٔ ۸: مقیاس‌پذیری و عملکرد (Scalability & Performance)

*   **Task Queue:** برای پردازش فایل‌های بزرگ و تماس‌های متعدد با API Gemini که می‌تواند زمان‌بر باشد، در نظر گرفتن یک Task Queue (مانند Celery با Redis یا RabbitMQ) برای پردازش‌های پس‌زمینه (Background Jobs) ضروری است. این کار زمان پاسخ‌گویی UI را بهبود می‌بخشد و از Timeout شدن درخواست‌ها جلوگیری می‌کند. سیستم می‌تواند پس از دریافت فایل، یک شناسهٔ کار (Job ID) برگرداند و کاربر از طریق آن وضعیت ترجمه را پیگیری کند.
*   **Streaming Responses:** استفاده از قابلیت Streaming در FastAPI برای ارسال به‌روزرسانی‌های پیشرفت به Frontend در زمان واقعی، که تجربه کاربری را بهبود می‌بخشد.
*   **بهینه‌سازی `python-pptx`:** بررسی و بهینه‌سازی عملیات‌های خواندن و نوشتن فایل‌های PPTX برای کاهش مصرف حافظه و زمان پردازش، به خصوص برای فایل‌های بسیار بزرگ.

### III.۹ مرحلهٔ ۹: تست و اعتبارسنجی (Testing Strategy)

*   **Unit Tests:** نوشتن تست‌های واحد جامع برای هر ماژول (مانند `PersonalityManager`, `SmartTextDetector`, `PersianFormatter`, `MedicalTranslationEngine`) برای اطمینان از صحت عملکرد هر جزء به صورت جداگانه و پوشش‌دهی موارد لبه.
*   **Integration Tests:** پیاده‌سازی تست‌های یکپارچه‌سازی برای بررسی تعامل صحیح بین ماژول‌های مختلف و اطمینان از جریان داده‌ها و منطق کسب‌وکار (مثلاً از دریافت فایل تا تولید خروجی نهایی).
*   **End-to-End Tests:** انجام تست‌های End-to-End با استفاده از فایل‌های PPTX واقعی و سناریوهای مختلف کاربری برای اطمینان از صحت ترجمه، حفظ فرمت‌بندی و تولید صحیح گزارش PDF در کل سیستم.
*   **Manual Review:** در نهایت، یک مرحله بازبینی دستی برای اطمینان از کیفیت ترجمه و فرمت‌بندی نهایی توسط یک متخصص.

---

## بخش IV: API Endpoints به‌روز شده

```
POST /api/v1/translate
├─ Request:
│  ├─ book_name: string (required)
│  ├─ file: file (required, .pptx)
│  ├─ field_override?: string (optional) - برای تشخیص حوزه بهتر
├─ Response (Streaming):
│  ├─ status: "researching" | "analyzing_pptx" | "translating" | "formatting" | "generating_report"
│  ├─ progress: 0-100
│  ├─ message: string
├─ Final Response:
│  ├─ status: "completed"
│  ├─ pptx_file_url: string
│  ├─ report_pdf_url: string
│  ├─ statistics: object
│  ├─ duration: integer

GET /api/v1/download/pptx/:file_id
GET /api/v1/download/report/:file_id
└─ Response: Binary files
```

---

## بخش V: نکات پیاده‌سازی کلیدی - بهبود یافته

### V.۱ بهبودهای جدید

۱. **Smart Text Detection:** تشخیص هوشمند متن‌های ترجمه‌پذیر
۲. **Domain Restriction:** مترجم فقط در حوزه تخصصی فعال است
۳. **Persian Fonts:** اعمال خودکار فونت B Nazanin
۴. **RTL Alignment:** راست‌چین کردن تمام متن‌ها
۵. **PDF Reports:** گزارش واژه‌های تخصصی
۶. **No Authentication:** عدم نیاز به لاگین
۷. **Temporary Server Storage:** فایل‌ها به صورت موقت روی سرور نگه‌داری شده و سپس حذف می‌شوند (حداکثر ۷ روز). این به معنای `STORAGE_MODE=server_temp` است.
۸. **Term Preservation:** حفظ کلمات تخصصی انگلیسی

### V.۲ تغییرات در UI

#### صفحهٔ نتیجه (به‌روز شده)

```
┌─────────────────────────────────────────┐
│  ✓ ترجمه با موفقیت انجام شد!          │
├─────────────────────────────────────────┤
│                                         │
│ 📊 آمار و اطلاعات:                     │
│  • منبع: Retinal Detachment             │
│  • حوزه: Ophthalmology                  │
│  • متن‌های ترجمه‌شده: ۳۴               │
│  • متن‌های دست‌نخورده: ۱۸              │
│  • واژه‌های تخصصی: ۲۸                   │
│                                         │
│ 📄 فایل‌های آماده دانلود:              │
│  ┌─────────────────────────────────┐   │
│  │ 📂 PowerPoint ترجمه‌شده        │   │
│  │ ✨ فرمت فارسی، راست‌چین        │   │
│  │ 📥 دانلود                      │   │
│  └─────────────────────────────────┘   │
│                                         │
│  ┌─────────────────────────────────┐   │
│  │ 📋 گزارش واژه‌ها (PDF)         │   │
│  │ 📖 لیست کامل اصطلاحات         │   │
│  │ 📥 دانلود                      │   │
│  └─────────────────────────────────┘   │
│                                         │
└─────────────────────────────────────────┘
```

---

## بخش VI: تغییرات در Environment Variables

```bash
# بدون authentication
AUTH_DISABLED=true

# فایل‌ها به صورت موقت روی سرور ذخیره شوند
STORAGE_MODE=server_temp

# گزارش PDF تولید شود
ENABLE_PDF_REPORTS=true

# محدودیت روزانه برداشته شد
API_RATE_LIMIT=unlimited

# فونت فارسی ثابت
DEFAULT_PERSIAN_FONT=B Nazanin

# راست‌چین اجباری
FORCE_RTL=true
```

---

## بخش VII: ملاحظات نهایی - به‌روز شده

پروژه اکنون **بسیار تخصصی‌تر و هوشمندتر** است:

✅ **Smart Translation:** فقط متن‌های توضیحی ترجمه شوند
✅ **Domain-Specific:** مترجم محدود به حوزه تخصصی
✅ **Persian Formatting:** فونت و راست‌چین خودکار
✅ **Term Reports:** گزارش PDF واژه‌ها
✅ **No Login Required:** رابط ساده و سریع
✅ **Temporary Server Storage:** فایل‌ها به صورت موقت روی سرور نگه‌داری شده و سپس حذف می‌شوند.
✅ **Term Preservation:** حفظ اصطلاحات تخصصی انگلیسی

**Timeline بهبود یافته:**
- **Phase 1:** Research + Smart PPTX Processing (۲ هفته)
- **Phase 2:** Specialized Translation Engine (۲ هفته)
- **Phase 3:** Persian Formatting + PDF Reports (۲ هفته)
- **Phase 4:** Integration + UI Polish (۲ هفته)
- **مجموع:** ۸ هفته (به جای ۱۹-۲۸ روز)

**آماده شروع هستیم! 🚀**
