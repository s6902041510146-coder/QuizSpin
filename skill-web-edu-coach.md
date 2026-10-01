# สกิลพัฒนาเว็บเพื่อการศึกษา — "EduCoach Workflow"

> สกิลนี้ออกแบบเพื่อพัฒนาเว็บแอปพลิเคชันทางการศึกษา โดยใช้ GitHub เป็นศูนย์กลางไฟล์ และ **Vercel AI Coach** เป็นผู้เชี่ยวชาญด้านโค้ด รอบคอบ และการออกแบบกราฟิก

---

## 1. วิสัยทัศน์และหลักการ

| หลักการ | คำอธิบาย |
|---------|----------|
| **โค้ดต้องอ่านง่าย** | โค้ดทุกบรรทัดต้องอ่านเข้าใจง่าย มี comment อธิบายที่จำเป็น |
| **ออกแบบก่อนเขียน** | ทุกหน้า/ฟีเจอร์ต้องมี wireframe → design mock → code เท่านั้น |
| **ทดสอบเป็นส่วนหนึ่งของโค้ด** | ทุกฟังก์ชันต้องมี test กำกับ ไม่มี test = ไม่ merge |
| **AI เป็นโค้ช ไม่ใช่นักเขียนโค้ด** | AI สอน ตรวจ แนะนำ แต่มนุษย์เป็นคนตัดสินใจสุดท้าย |
| **Performance & Accessibility เป็น priority** | Core Web Vitals ดี และรองรับ WCAG 2.1 AA เป็น baseline |

---

## 2. สถาปัตยกรรมเทคโนโลยี (Tech Stack)

### 2.1 กรอบงานหลัก
```
Frontend     →  Next.js 15 (App Router) + React 19 + TypeScript
Styling      →  Tailwind CSS v4 + shadcn/ui + Framer Motion
State        →  Zustand (client) + React Query (server)
Backend      →  Next.js API Routes / Server Actions
Database     →  PostgreSQL (Supabase) + Prisma ORM
Auth         →  NextAuth.js v5 (Google, Email, และ Institution SSO)
Storage      →  Supabase Storage (รูป, วิดีโอ, เอกสาร)
Deployment   →  Vercel (Production + Preview + Development)
Monitoring   →  Vercel Analytics + Sentry
AI Coach     →  Vercel AI SDK + Custom Skill Prompts
```

### 2.2 ทำไมต้อง Vercel?
- **AI SDK**: พร้อมใช้สำหรับสร้าง AI Coach (chat, suggestions, code review)
- **Preview Deployments**: ทุก PR มี URL ทดสอบ → โค้ชตรวจสอบก่อน merge
- **Edge Functions**: response เร็ว เหมาะกับเว็บเพื่อการศึกษาที่มีผู้ใช้กระจายทั่วโลก
- **Fluid Compute**: รองรับ spike ช่วงสอบได้ดี

### 2.3 GitHub เป็นศูนย์กลาง
```
Repository Structure:
├── .github/
│   ├── workflows/          # CI/CD, Auto-label, Preview deploy
│   ├── ISSUE_TEMPLATE/     # Bug, Feature, Content request
│   └── CODEOWNERS
├── .vercel/
│   └── project.json
├── src/
│   ├── app/                # Routes (App Router)
│   ├── components/         # UI components
│   │   ├── ui/             # shadcn/ui primitives
│   │   ├── features/       # Domain components (Quiz, Lesson, ...)
│   │   └── layout/         # Nav, Footer, Sidebar
│   ├── lib/                # Utils, constants, types
│   ├── hooks/              # Custom React hooks
│   ├── server/             # Server actions, API logic
│   └── styles/             # Global styles, themes
├── prisma/
│   └── schema.prisma
├── public/                 # Static assets
├── docs/                   # Architecture decisions (ADRs)
├── tests/
│   ├── unit/               # Vitest
│   ├── integration/         # Playwright
│   └── e2e/                # Playwright user flows
├── scripts/                # Seed, generate, migrate
├── .skill/                  # AI Coach prompts & rules (ดูส่วน 4)
├── next.config.ts
├── tailwind.config.ts
└── tsconfig.json
```

---

## 3. Vercel AI Coach — การทำงาน

### 3.1 รูปแบบโค้ช 3 รูปแบบ

#### A. Code Review Coach (อัตโนมัติ)
```
เมื่อมี PR ใหม่:
1. GitHub Action เรียก Vercel AI SDK
2. AI วิเคราะห์ diff → เช็ค:
   - ความถูกต้องของ logic
   - Performance (re-render, bundle size)
   - Accessibility (aria labels, contrast)
   - Security (XSS, SQL injection)
3. Comment ใน PR พร้อม suggestion
4. หากพบปัญหาร้ายแรง → block merge
```

#### B. Design Coach (เรียลไทม์)
```
ระหว่างพัฒนา:
1. มี Chat panel ใน VS Code หรือเบราว์เซอร์ (ใช้ Vercel AI Chat)
2. ผู้พัฒนาแนะนำ requirement → Coach แนะนำ:
   - Component ที่ควรใช้
   - Layout pattern ที่เหมาะสม
   - Animation ที่เพิ่ม UX
   - สี, typography, spacing ตาม design system
3. Coach generate mockup หรือ code stub ให้
```

#### C. Learning Coach (สำหรับผู้เรียน)
```
ในตัวแอปเว็บเพื่อการศึกษา:
1. เลือกระดับความยาก (adaptive)
2. AI ปรับเนื้อหาตาม performance
3. มี Socratic questioning (แนะนำแทนบอกคำตอบ)
4. Feedback แบบ formative ทุกขั้นตอน
5. เก็บข้อมูล learning analytics
```

### 3.2 วิธีสร้าง Coach Bot (ตัวอย่าง)

```typescript
// .skill/coach/code-review.ts
import { generateText } from 'ai';
import { openai } from '@ai-sdk/openai';

export const codeReviewCoach = async (diff: string, context: string) => {
  const { text } = await generateText({
    model: openai('gpt-4o'),
    system: `คุณคือ Senior Software Engineer ที่เชี่ยวชาญ React, TypeScript, Next.js และการออกแบบระบบเพื่อการศึกษา
    หน้าที่ของคุณ:
    1. ตรวจสอบความถูกต้องและความปลอดภัย
    2. แนะนำวิธีที่ดีกว่า พร้อมเหตุผล
    3. เช็ค accessibility และ performance
    4. ให้ feedback ที่สร้างสรรค์ ไม่ใช่แค่วิจารณ์
    5. ถ้าโค้ดดี ก็ชมเชย เพื่อสร้างแรงบันดาลใจ
    
    Format: 
    - สรุปปัญหา (ถ้ามี) เป็น bullet points
    - แนะนำแนวทางแก้ไข
    - ให้ score: 1-10 พร้อมเหตุผล`,
    prompt: `โปรดรีวิวโค้ดนี้:\n\nContext: ${context}\n\nDiff:\n${diff}`,
  });
  return text;
};
```

---

## 4. Skill สำหรับ AI Coach (`.skill/`)

### 4.1 โครงสร้าง skill prompts
```
.skill/
├── coach/
│   ├── code-review.ts       # รีวิวโค้ดอัตโนมัติ
│   ├── design-system.ts     # ตรวจสอบ design consistency
│   ├── learning-path.ts     # ออกแบบเส้นทางการเรียนรู้
│   └── qa-checker.ts        # ตรวจสอบ content quality
├── templates/
│   ├── lesson.md.template   # โครงสร้างบทเรียน
│   ├── quiz.json.schema     # Schema สำหรับ quiz
│   └── ui-component.tsx.template
└── rules/
    ├── typescript-rules.md
    ├── accessibility-rules.md
    └── performance-budget.md
```

### 4.2 ตัวอย่าง rules สำหรับ Coach

```markdown
<!-- .skill/rules/design-system.md -->

## Design Tokens (ห้ามเปลี่ยนแปลงโดยไม่ผ่านการพิจารณา)
- Font: 'Prompt', 'Sarabun' (สลับกันตาม context)
- Color Palette:
  - Primary: Indigo-600 (#4F46E5)
  - Secondary: Emerald-600
  - Accent: Amber-500
  - Neutral: Slate
- Spacing: เป็นเลขคู่ของ 4 (4, 8, 12, 16, 24, 32, 48, 64)
- Border radius: 8px (component), 16px (card), 24px (modal)
- Shadow: ใช้จาก Tailwind preset เท่านั้น

## Component Rules
- Button: มี hover, focus, active, disabled state ครบ
- Form: มี label, error message, helper text, loading state
- Modal: มี backdrop blur, ESC to close, focus trap
- Table: รองรับ sort, filter, pagination, empty state

## Motion Guidelines
- Duration: 200ms (micro), 300ms (standard), 500ms (complex)
- Easing: ease-out (enter), ease-in (exit), spring (interactive)
- ห้ามใช้ animation ที่ไม่มี functional purpose

## Accessibility Rules
- ทุกรูปภาพต้องมี alt text
- Form ทุกช่องต้องมี label (แม้จะซ่อนด้วย sr-only)
- ต้องใช้ได้ด้วย keyboard ล้วน
- Focus indicator ต้องมองเห็นได้ชัด
```

---

## 5. Workflow การพัฒนา (Development Lifecycle)

### 5.1 วงจรการพัฒนา Feature

```
[1] REQUIREMENT
    ├── เขียน issue ใน GitHub ตาม template
    ├── ระบุ: วัตถุประสงค์, user story, acceptance criteria
    └── AI Coach ช่วย refine requirement (ถ้าต้องการ)

[2] DESIGN
    ├── สร้าง wireframe (Figma หรือ Mermaid diagram)
    ├── Design Coach เช็ค: layout, spacing, color, typography
    ├── ทำ interactive prototype (Framer)
    └── Review โดย team + Design Coach

[3] IMPLEMENTATION
    ├── สร้าง branch: feat/lesson-quiz-system
    ├── เขียนโค้ดตาม design ที่ approve
    ├── ระหว่างเขียน: ถาม Code Coach ได้ตลอด (VS Code extension)
    ├── Pre-commit: lint, type-check, test
    └── Push → GitHub Action ทำงาน

[4] AUTOMATED REVIEW
    ├── Code Review Coach วิเคราะห์ diff
    ├── Accessibility Coach ตรวจสอบ
    ├── Performance Coach เช็ค Core Web Vitals
    ├── Security Coach สแกนช่องโหว่
    └── ผลลัพธ์ → PR comment + status check

[5] PREVIEW TESTING
    ├── Vercel สร้าง preview URL อัตโนมัติ
    ├── ทดสอบบน: Desktop, Tablet, Mobile
    ├── ทดสอบ accessibility: screen reader, keyboard nav
    └── Playwright E2E test รัน

[6] MERGE & DEPLOY
    ├── ผ่าน review ทั้งหมด → merge to main
    ├── Vercel auto-deploy to production
    ├── Smoke test หลัง deploy
    └── ตรวจสอบ analytics 24 ชั่วโมงแรก
```

### 5.2 GitHub Actions สำหรับ Coach

```yaml
# .github/workflows/ai-coach.yml
name: AI Coach Review
on: [pull_request]

jobs:
  code-review:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: Code Review Coach
        uses: vercel/ai-coach-action@v1
        with:
          skill: '.skill/coach/code-review.ts'
          model: 'gpt-4o'
      - name: Accessibility Check
        uses: vercel/ai-coach-action@v1
        with:
          skill: '.skill/rules/accessibility-rules.md'
      - name: Performance Budget
        run: npx bundlesize
```

---

## 6. การออกแบบเว็บเพื่อการศึกษา — UX Principles

### 6.1 หลักการทางการศึกษา
| หลัก | การนำไปใช้ |
|------|-------------|
| **Scaffolding** | เรียนจากง่าย→ยาก มี hint และแนะนำ |
| **Spaced Repetition** | ทบทวนเนื้อหาด้วย interval ที่เหมาะสม |
| **Active Recall** | ใช้ quiz, flashcard มากกว่าอ่านซ้ำ |
| **Multimedia Learning** | ภาพ+เสียง+ข้อความ ให้เลือกได้ตาม learning style |
| **Mastery Learning** | ต้องผ่านเกณฑ์ก่อนถึงจะไปหน้าถัดไป |
| **Formative Feedback** | feedback ทันทีทุกขั้นตอน |

### 6.2 โครงสร้างหน้าเว็บ

```
/                      → Landing page (ประสบการณ์แรก)
/learn                 → แดชบอร์ดการเรียนรู้
/learn/[course]        → หน้าคอร์ส
/learn/[course]/[lesson] → บทเรียน (interactive)
/practice             → โหมดฝึกหัด
/assessment            → การวัดผล
/analytics             → สถิติการเรียนรู้
/ai-tutor              → คุยกับ AI Tutor
/settings              → ตั้งค่า (theme, notification, accessibility)
```

### 6.3 Layout Pattern แนะนำ

```
┌─────────────────────────────────────────┐
│  Nav: Logo | Search | Progress | Avatar │
├──────────┬──────────────────────────────┤
│          │                              │
│ Sidebar  │    Main Content Area         │
│ - Course │    (Lesson / Practice /      │
│ - Chapters│     Assessment / Analytics) │
│ - Progress│                              │
│          │                              │
│          │  ┌────────────────────────┐  │
│          │  │  AI Coach Panel       │  │
│          │  │  (Toggleable)         │  │
│          │  └────────────────────────┘  │
└──────────┴──────────────────────────────┘
```

---

## 7. Testing Strategy

### 7.1 Testing Pyramid
```
            ┌─────────┐
           /   E2E    \       ← Playwright (critical paths)
          /─────────────\
         /  Integration  \     ← API + Component (MSW + RTL)
        /─────────────────\
       /      Unit Tests    \   ← Vitest (utils, hooks, lib)
      /───────────────────────\
```

### 7.2 เครื่องมือและตัวอย่าง

```typescript
// tests/unit/quiz-logic.test.ts
import { describe, it, expect } from 'vitest';
import { calculateScore, generateAdaptiveQuiz } from '@/lib/quiz';

describe('Quiz Engine', () => {
  it('calculates score correctly', () => {
    const answers = [true, false, true, true];
    expect(calculateScore(answers)).toBe(75);
  });

  it('adapts difficulty based on performance', () => {
    const easyStudent = generateAdaptiveQuiz({ accuracy: 0.9 });
    expect(easyStudent.difficulty).toBe('medium');
    
    const strugglingStudent = generateAdaptiveQuiz({ accuracy: 0.4 });
    expect(strugglingStudent.difficulty).toBe('easy');
    expect(strugglingStudent.hasHints).toBe(true);
  });
});
```

```typescript
// tests/e2e/learning-flow.spec.ts
import { test, expect } from '@playwright/test';

test('complete a lesson end-to-end', async ({ page }) => {
  await page.goto('/login');
  await page.fill('[name="email"]', 'student@test.com');
  await page.fill('[name="password"]', 'password');
  await page.click('button[type="submit"]');
  
  await page.goto('/learn/math/algebra/linear-equations');
  await expect(page.locator('h1')).toContainText('สมการเชิงเส้น');
  
  // Interact with lesson
  await page.click('button[data-testid="start-lesson"]');
  await page.waitForSelector('[data-testid="lesson-content"]');
  
  // Complete quiz
  await page.click('[data-testid="take-quiz"]');
  await page.click('[data-correct="true"]');
  await expect(page.locator('[data-testid="feedback"]')).toBeVisible();
});
```

---

## 8. Design System สำหรับเว็บเพื่อการศึกษา

### 8.1 Color System (ตัวอย่าง)
```css
:root {
  /* Primary - สีหลักที่ใช้บ่อย */
  --primary-50: #EEF2FF;
  --primary-100: #E0E7FF;
  --primary-500: #6366F1;
  --primary-600: #4F46E5;
  --primary-700: #4338CA;
  
  /* Success - เมื่อตอบถูก */
  --success-500: #22C55E;
  --success-100: #DCFCE7;
  
  /* Error - เมื่อตอบผิด */
  --error-500: #EF4444;
  --error-100: #FEE2E2;
  
  /* Warning - เมื่อใกล้หมดเวลา */
  --warning-500: #F59E0B;
  --warning-100: #FEF3C7;
  
  /* Neutral - พื้นหลังและข้อความ */
  --neutral-50: #F8FAFC;
  --neutral-100: #F1F5F9;
  --neutral-500: #64748B;
  --neutral-900: #0F172A;
  
  /* Learning-specific */
  --learning-started: #3B82F6;    /* เริ่มเรียน */
  --learning-progress: #8B5CF6;   /* กำลังเรียน */
  --learning-complete: #10B981;   /* จบบทเรียน */
  --learning-review: #F59E0B;     /* ต้องทบทวน */
}
```

### 8.2 Typography
```css
/* หัวข้อ */
.heading-1: 'Prompt', 32px, weight 700, line-height 1.3
.heading-2: 'Prompt', 24px, weight 600, line-height 1.4
.heading-3: 'Prompt', 20px, weight 600, line-height 1.5

/* เนื้อหา */
.body: 'Sarabun', 16px, weight 400, line-height 1.7
.body-sm: 'Sarabun', 14px, weight 400, line-height 1.6

/* UI */
.label: 'Sarabun', 14px, weight 500, line-height 1.5
.caption: 'Sarabun', 12px, weight 400, line-height 1.5
```

### 8.3 Component Library แนะนำ

```tsx
// Example: Interactive Quiz Component
// src/components/features/Quiz/MultipleChoiceQuiz.tsx

interface MultipleChoiceQuizProps {
  question: string;
  options: {
    id: string;
    text: string;
    image?: string;
  }[];
  correctAnswer: string;
  explanation: string;
  difficulty: 'easy' | 'medium' | 'hard';
  onAnswer: (isCorrect: boolean, timeSpent: number) => void;
  hints?: string[];
}

export function MultipleChoiceQuiz({
  question,
  options,
  correctAnswer,
  explanation,
  difficulty,
  onAnswer,
  hints,
}: MultipleChoiceQuizProps) {
  const [selected, setSelected] = useState<string | null>(null);
  const [showHint, setShowHint] = useState(false);
  const [showExplanation, setShowExplanation] = useState(false);
  const startTime = useRef(Date.now());

  const handleSubmit = () => {
    if (!selected) return;
    const isCorrect = selected === correctAnswer;
    const timeSpent = (Date.now() - startTime.current) / 1000;
    setShowExplanation(true);
    onAnswer(isCorrect, timeSpent);
  };

  return (
    <div className="w-full max-w-2xl mx-auto p-6 rounded-2xl bg-white shadow-lg">
      {/* Difficulty Badge */}
      <div className="flex justify-between items-center mb-4">
        <Badge variant={difficultyColor[difficulty]}>
          {difficultyLabel[difficulty]}
        </Badge>
        {hints && (
          <Button 
            variant="ghost" 
            size="sm"
            onClick={() => setShowHint(!showHint)}
          >
            <Lightbulb className="w-4 h-4 mr-1" />
            {showHint ? 'ซ่อนคำใบ้' : 'ขอคำใบ้'}
          </Button>
        )}
      </div>

      {/* Question */}
      <h3 className="text-xl font-semibold mb-6 text-neutral-900">
        {question}
      </h3>

      {/* Hint Panel */}
      {showHint && hints && (
        <motion.div
          initial={{ opacity: 0, y: -10 }}
          animate={{ opacity: 1, y: 0 }}
          className="mb-4 p-4 rounded-xl bg-amber-50 border border-amber-200"
        >
          <p className="text-sm text-amber-800">
            💡 {hints[0]}
          </p>
        </motion.div>
      )}

      {/* Options */}
      <div className="space-y-3">
        {options.map((option) => (
          <motion.button
            key={option.id}
            whileHover={{ scale: 1.02 }}
            whileTap={{ scale: 0.98 }}
            onClick={() => setSelected(option.id)}
            className={cn(
              "w-full p-4 rounded-xl border-2 text-left transition-all",
              selected === option.id
                ? "border-primary-500 bg-primary-50"
                : "border-neutral-200 hover:border-neutral-300"
            )}
          >
            <div className="flex items-center gap-3">
              <div className={cn(
                "w-6 h-6 rounded-full border-2 flex items-center justify-center",
                selected === option.id
                  ? "border-primary-500 bg-primary-500"
                  : "border-neutral-300"
              )}>
                {selected === option.id && (
                  <Check className="w-4 h-4 text-white" />
                )}
              </div>
              <span className="flex-1 text-neutral-900">{option.text}</span>
            </div>
          </motion.button>
        ))}
      </div>

      {/* Explanation */}
      {showExplanation && (
        <motion.div
          initial={{ opacity: 0, height: 0 }}
          animate={{ opacity: 1, height: 'auto' }}
          className="mt-4 p-4 rounded-xl bg-blue-50 border border-blue-200"
        >
          <p className="text-sm font-medium text-blue-900 mb-1">
            {selected === correctAnswer ? '✅ ถูกต้อง!' : '❌ ไม่ถูกต้อง'}
          </p>
          <p className="text-sm text-blue-800">{explanation}</p>
        </motion.div>
      )}

      {/* Submit */}
      <Button
        className="mt-6 w-full"
        disabled={!selected || showExplanation}
        onClick={handleSubmit}
      >
        {showExplanation ? 'ต่อไป' : 'ตรวจสอบคำตอบ'}
      </Button>
    </div>
  );
}
```

---

## 9. การวัดผลและประเมินผล

### 9.1 Learning Analytics
```typescript
// Tracking events สำหรับวิเคราะห์การเรียนรู้
interface LearningEvent {
  type: 'lesson_start' | 'lesson_complete' | 'quiz_attempt' | 
        'quiz_correct' | 'hint_used' | 'time_spent' | 'revisit';
  userId: string;
  lessonId: string;
  data: {
    score?: number;
    timeSpent?: number;
    attempts?: number;
    hintsUsed?: number;
    difficulty?: string;
  };
  timestamp: Date;
}
```

### 9.2 Dashboard สำหรับครู
```
┌─────────────────────────────────────────────────┐
│  Class Performance Overview                     │
├────────────────┬────────────────┬───────────────┤
│  Average Score │ Completion Rate│ Time Spent    │
│     85%        │     78%        │   24h 30m     │
├────────────────┴────────────────┴───────────────┤
│                                                 │
│  Student Progress Heatmap                       │
│  [██████░░░░] Advanced    25%                   │
│  [████████░░] Proficient  40%                   │
│  [██████░░░░] Basic       20%                   │
│  [░░░░░░░░░░] Struggling  15%                   │
│                                                 │
│  Recommended Actions:                           │
│  • 3 students need review on linear equations   │
│  • 2 students ready for advanced topics         │
│  • Overall class ready for quiz next week       │
└─────────────────────────────────────────────────┘
```

---

## 10. Checklist ก่อน Launch

### ด้านเทคนิค
- [ ] Lighthouse score > 90 ทุกเพจ
- [ ] Core Web Vitals ผ่านเกณฑ์ (LCP < 2.5s, FID < 100ms, CLS < 0.1)
- [ ] Accessibility: ผ่าน WCAG 2.1 AA
- [ ] Responsive: ทดสอบแล้วบน Mobile, Tablet, Desktop
- [ ] Security: ผ่าน OWASP Top 10 review
- [ ] Performance budget: JS < 200KB, CSS < 50KB (gzipped)
- [ ] Error tracking: Sentry ตั้งค่าแล้ว
- [ ] Analytics: ติดตาม user flows ได้ครบ
- [ ] i18n: รองรับภาษาไทย + อังกฤษ
- [ ] SEO: Meta tags, Open Graph, structured data

### ด้านการศึกษา
- [ ] เนื้อหาได้รับการตรวจโดยผู้เชี่ยวชาญ
- [ ] Learning objectives ชัดเจนและวัดผลได้
- [ ] Feedback ทุกขั้นตอนมีประโยชน์
- [ ] มีการปรับระดับความยากตามผู้เรียน
- [ ] ทดสอบกลุ่มเป้าหมายแล้ว (pilot)

### ด้าน Design
- [ ] Design system มี consistency
- [ ] Dark mode รองรับ
- [ ] Animation ไม่รบกวนการเรียนรู้
- [ ] Empty states มีข้อความแนะนำ
- [ ] Loading states สวยงาม
- [ ] Error states เป็นมิตรและบอกวิธีแก้

---

## 11. สรุป: วิธีเริ่มต้นใช้งาน

### ขั้นตอนที่ 1: ตั้งค่า Repository
```bash
# สร้างโปรเจกต์
npx create-next-app@latest my-edu-platform --typescript --tailwind --app
cd my-edu-platform

# ติดตั้ง dependencies
npm install prisma @prisma/client next-auth @auth/prisma-adapter
npm install @tanstack/react-query zustand framer-motion
npm install shadcn-ui @radix-ui/react-dialog @radix-ui/react-tabs
npm install ai @ai-sdk/openai @ai-sdk/anthropic

# ตั้งค่า GitHub + Vercel
git init
git remote add origin git@github.com:your-org/my-edu-platform.git
git push -u origin main

# เชื่อม Vercel (ผ่าน CLI หรือ UI)
npx vercel
```

### ขั้นตอนที่ 2: ตั้งค่า AI Coach
```bash
# สร้างไฟล์ skill
mkdir -p .skill/coach
# คัดลอก prompts จากส่วน 3 และ 4 ในเอกสารนี้

# ตั้งค่า environment variables
echo "OPENAI_API_KEY=sk-..." >> .env.local
echo "DATABASE_URL=postgresql://..." >> .env.local
echo "NEXTAUTH_SECRET=..." >> .env.local
```

### ขั้นตอนที่ 3: เริ่มพัฒนาแรก
1. สร้าง issue แรกใน GitHub: "Setup design system foundation"
2. ใช้ Design Coach เพื่อวาง design tokens
3. สร้าง component library เริ่มจาก primitives
4. ทดสอบ workflow กับ feature เล็กๆ ก่อน

---

## 12. แหล่งข้อมูลเพิ่มเติม

- [Next.js Documentation](https://nextjs.org/docs)
- [Vercel AI SDK](https://sdk.vercel.ai/)
- [Tailwind CSS](https://tailwindcss.com/)
- [shadcn/ui](https://ui.shadcn.com/)
- [Prisma](https://www.prisma.io/)
- [Supabase](https://supabase.com/)
- [Web Content Accessibility Guidelines (WCAG)](https://www.w3.org/WAI/standards-guidelines/wcag/)
- [Learning Engineering](https://www.learntechlib.org/)

---

*สกิลนี้ออกแบบโดย Vercel AI Coach — อัปเดตล่าสุด: 2026*
