# UX Writing Rules (Korean)

Product copy must feel like a calm, friendly person talking — not a system, not a salesperson.

## 1. 해요체 everywhere
- Every string in the product: titles, buttons, toasts, errors, empty states.
- O "저장했어요" / X "저장되었습니다" / X "저장 완료!"

## 2. Active voice
- Say what the user (or the app) did, not what "was done".
- "기록됐어요" → "기록했어요"
- "삭제 처리되었습니다" → "지웠어요"

## 3. Positive framing + next step
- Don't stop at "안 돼요 / 없어요". Say how it can work.
- X "기록이 없어요" → O "아직 기록이 없어요. 아래 버튼으로 첫 기록을 남길 수 있어요"
- X "네트워크 오류" → O "인터넷 연결을 확인하고 다시 시도해 주세요"

## 4. Light honorifics
- Drop "~시~", "~께", "계시다", "여쭈다".
- "확인하시겠어요?" → "확인할까요?"
- "고객님께" → "회원님에게" (or restructure the sentence)

## 5. Break noun piles into verbs
- "비밀번호 변경 완료" → "비밀번호를 바꿨어요"
- "알림 수신 동의" → "알림 받기"

## 6. Buttons predict the next action
- Label = verb phrase describing exactly what happens: "~하기" or "~할게요" style is fine; pick one style per product and keep it.
- O "물 마신 거 기록하기", "이 목표로 저장하기", "친구에게 보내기"
- X "확인", "다음", "시작", "공유" (too vague when used as the main CTA)
- If the label includes a value that changes, show it: "350ml 기록하기".
- Disabled CTA may explain what's missing: "컵 크기를 골라주세요".

## 7. Dialog buttons
- Left (dismiss) = always **"닫기"**. Never "취소" (sounds like canceling an action already in progress).
- Right = the concrete action: "지우기", "로그아웃하기", "보내기".
- Title = a question that names the object: "오전 8:12 기록을 지울까요?"
- Description = consequence, one line: "지운 기록은 되돌릴 수 없어요".

## 8. Toasts
- Past tense, one line, ≤ 20 characters when possible: "저장했어요", "링크를 복사했어요".
- No exclamation marks, no emoji.

## 9. Numbers and units
- Thousands separator: 1,250ml / 12,000원.
- No space between number and unit for ml, kg, %, 원; space for words: "3 잔" X → "3잔" O.
- Dates: "11월 18일", times: "오전 8:12" or "08:12" — pick one per product.

## 10. Banned patterns (dark patterns)
- No fake urgency ("3분 남았어요!" without a real deadline).
- No guilt/shaming opt-outs ("아니요, 건강은 신경 안 쓸래요").
- No pleading or sad graphics to keep users.
- No surprise costs; show price before the action.
- Always give a way out next to every CTA (닫기 / 나중에 하기).
- No pop-up or bottom sheet on page entry, and none triggered by the back button.

## 11. Tone guardrails
- No slang, memes, or excessive trendy words.
- No exclamation marks in product UI (marketing pages may use them sparingly).
- Emoji: none in UI chrome; allowed only in user-generated content.
