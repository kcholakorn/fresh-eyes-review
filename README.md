# fresh-eyes-review

> A Claude Code skill that simulates **real human readers** reading your
> handover / onboarding / handbook docs — so you catch what makes the
> receiving team bounce it back **before** you hit send.

จำลองคนในทีมหลายคน (เลือกตำแหน่ง + ประสบการณ์ได้) มาอ่านเอกสารแบบมนุษย์จริง —
เหนื่อยเป็น จำไม่หมด หลงเป็น — เพื่อจับจุดที่ทีมผู้รับจะ **งง / ย้อนอ่าน / ถามกลับ /
ตีกลับ** ("ส่งอะไรมาวะเนี่ย") แล้วแก้ก่อนส่งจริง.

---

## ทำไมถึงมี skill นี้

เขียนเอกสาร handover เสร็จแล้วไม่รู้ว่า "คนจริง" อ่านแล้วรู้เรื่องไหม ก่อนส่งทีม dev/QA.
แทนที่จะเดา — ให้ Claude สวมบทผู้อ่านหลายคน (personas) อ่านแบบมนุษย์จริง (อ่านทีละไฟล์
ตามลำดับ, ความจำจำกัด, เหนื่อย/หลงเป็น) แล้วรายงานว่าจุดไหนพัง.

## What it catches

- **Per-persona comprehension** — แต่ละตำแหน่ง/ประสบการณ์ เข้าใจได้กี่ %, ตรงไหนงง
- **Friction points** — ไฟล์/บรรทัด/หัวข้อที่ทำให้หยุด ย้อนอ่าน หรือหลงทาง
- **Reading fatigue & flow** — ตรงไหนเริ่มล้า, ตารางยาวไหวไหม, ลิงก์เยอะจนหลง thread ไหม
- **Doc-vs-code drift** (full-repo mode) — `file:line` / ค่า / ลิงก์ ที่อ้าง ตรงกับของจริงไหม
- **Undecodable internal jargon** — รหัส/ตัวย่อ/ศัพท์ภายใน (เช่น `M28b`, `H-beam`) ที่คนนอก
  ถอดไม่ออกเพราะไม่มี glossary

**Read-only** — รายงานอย่างเดียว ไม่แก้ไฟล์ ไม่เปิด PR ไม่เสนอ diff.

## โหมดการอ่าน

| โหมด | ชื่อ | อ่านอะไรได้ |
|------|------|-------------|
| **A** | Blind / doc-only | เฉพาะโฟลเดอร์เอกสารที่ระบุ — ทดสอบว่าเอกสารยืนได้ด้วยตัวเองไหม |
| **B** | Full repo | เปิด code/vault/config ได้ + ลองทำงานจริง + จับ drift |
| **C** | ทั้งสอง | รัน A แล้ว B เทียบผล |

## Install

Skill นี้คือโฟลเดอร์ `fresh-eyes-review/` ที่มี `SKILL.md` อยู่ข้างใน — เอาไปวางในที่ที่
Claude Code อ่าน skill:

**Global (ใช้ข้ามทุกโปรเจกต์):**
```bash
git clone https://github.com/kcholakorn/fresh-eyes-review.git \
  ~/.claude/skills/fresh-eyes-review
```

**Project (เฉพาะ repo เดียว):**
```bash
git clone https://github.com/kcholakorn/fresh-eyes-review.git \
  <your-repo>/.claude/skills/fresh-eyes-review
```

> โครงสร้างที่ Claude Code ต้องการคือ `<skills-dir>/fresh-eyes-review/SKILL.md`.
> repo นี้วาง `SKILL.md` ไว้ที่ root อยู่แล้ว เลย clone ลงไปตรงๆ ได้เลย.

## Usage

เปิด Claude Code ในโปรเจกต์ที่มีเอกสารจะรีวิว แล้วพิมพ์อะไรก็ได้แนวนี้:

```
/fresh-eyes-review
```
หรือพูดเป็นภาษาคน:
```
ลอง fresh eyes เอกสารใน docs/handover/ หน่อย ว่าคนใหม่อ่านแล้วรู้เรื่องไหม
```

Skill จะ:
1. ถามโหมด (A / B / C)
2. ถามว่าให้ใครอ่านบ้าง (personas — ตำแหน่ง + ประสบการณ์) หรือใช้ default 3 คน
3. อ่านเอกสารแบบมนุษย์จริง ทีละ persona
4. ออกรายงานต่อ persona + สรุปรวม "ทีมจะตีกลับไหม" + fix list เรียงตาม impact/effort

## License

[MIT](LICENSE) © 2026 Cholakorn Kokulkiat
