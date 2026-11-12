# Spec 0006 — UI işlerinde ANALYZE tasarım artifact'ı üretir

- Status: In progress
- Mode: lite (ANEW's own development)
- Plan: `.anew-dev/plans/0006-plan.md`
- Self-critique: done (G-1…G-6 folded in — Selin Öztürk, 2026-11-12)

## Intent
UI içeren işlerde spec yalnızca metin olduğu için ekranların görünümü plan ve build aşamasına
kadar belirsiz kalıyor; VERIFY'daki ekran görüntüsü onaylanmış bir görselle değil, yazılı bir
akışla karşılaştırılıyor. ANEW-0008 bu durumu kabul etmiş, "tasarım onayının ANALYZE bitmeden
ayrı bir insan kapısı olarak istenmesi"ni yeniden değerlendirme tetikleyicisi olarak yazmıştı —
şirket içi ekipler bu ihtiyacı dile getirdi (Selin Öztürk, 2026-11-12).

Başarı: UI içeren her spec, ANALYZE'da ajanın çizdiği (ya da var olan onaylı bir tasarıma
bağlanan) bir tasarım artifact'ıyla birlikte onaya gelir; artifact kabul kriterlerinin dokunduğu
her ekranı ve durumu kapsar; spec artifact'ın sabit sürümüne standart bir alanla bağlanır; spec
onayı spec + artifact'ın birlikte onayıdır; `doctor` bu alanı arar.

Bilerek yapılmayanlar: görsel kalitenin otomatik değerlendirilmesi; tasarım dosyalarının repoya
konması (ANEW-0006); yeni segment, rol ya da kapı (onay mevcut spec kapısında genişler); core'da
belirli bir aracın adı (ANEW-0011 — araç adapter'da); şablonun Türkçeleştirilmesi (0007).

## Requirements
1. **Karar kaydı.** Yeni ADR `docs/decisions/anew/ANEW-0013` bu kuralı kayda geçirir ve
   ANEW-0008'i günceller (0008'e `Revised:` satırı; karar metni korunur, 0013'e işaret eder).
2. **Kural (core, araç adı vermeden).** Spec'in `User experience` bölümü "none — no user
   interface" değilse Analyst, ANALYZE'da sürümlü ve adresle açılabilen bir **tasarım artifact'ı**
   üretir:
   - kabul kriterlerinde geçen her ekranı ve o ekranın listelenen her durumunu (boş, yükleniyor,
     hata, başarılı, yetki yok — spec'te hangileri varsa) çizer;
   - her çizim ilgili kriter numarasıyla etiketlenir (ör. `AC-3`);
   - zaten onaylı bir tasarım varsa (ör. tasarımcının dosyası) yeniden çizmez, onun sabit
     sürümüne bağlanır — aynı ekranın iki kaynağı olmaz.
   `prompts/spec.md`'deki "Never invent visuals" bu kuralla değişir: ajan çizer, çizim ancak spec
   kapısında onaylanınca tasarım sayılır. `prompts/clarify.md` UI işinde "tasarım var mı?"
   sorusunu artifact kararına bağlar.
3. **Standart alan.** `specs/TEMPLATE.md` `User experience` bölümünde tek biçimli bir satır:
   `- Design artifact: <adres> (version <id>)`. `specs/TEMPLATE-mini.md` `Design:` alanı UI
   değişikliğinde aynı biçimi kullanır ve yalnızca değişen ekranları kapsar.
4. **Onay kapısı.** `workflows/segments.md` ANALYZE kapısı: spec onayı, spec ile alandaki artifact
   sürümünün **birlikte** onayıdır; artifact eksik ya da kriterlerden birinin ekranı/durumu
   çizilmemişse spec onaya sunulmaz. Onaydan sonra artifact'ta değişiklik = spec değişikliği (R-08)
   ve alandaki sürüm güncellenir. Onaylanan sürüm sonradan açılabilir olmalıdır: araç eski bir
   sürümü kimliğiyle açamıyorsa onaylanan sürüm dondurulur, her değişiklik yeni sürüm olarak
   yayınlanır ve alan güncellenir (G-6; plan ilk adımda Claude artifact'ında dener).
5. **VERIFY.** `prompts/verify.md` ve `docs/testing.md` (Evidence for UI criteria): UI kriterinin
   ekran görüntüsü, alandaki artifact sürümündeki aynı etiketli çizimle karşılaştırılır.
6. **Araç (adapter).** Claude Code adapter'ı (`adapters/claude-code/`) artifact'ı Claude
   artifact'ı olarak üretmeyi ve alanına artifact adresi + sürüm kimliğini yazmayı söyler; spec
   kapısında artifact'ın ekiple/organizasyonla paylaşılmasını hatırlatır — artifact'lar sahibine
   özel başlar ve ajan paylaşımı değiştiremez, insan paylaşır (G-5). Diğer
   adapter'lar core'daki araç bağımsız kuralı kullanır; adapter paritesi korunur.
7. **Zorlama (`scripts/doctor`).** `specs/active/` içindeki `Approved` ya da `In progress` bir
   spec'te `User experience` bölümü "none — no user interface" değilse ve geçerli bir
   `Design artifact:` satırı yoksa WARN; `--strict`'te FAIL. Yalnızca bu tek alan okunur
   (B-3/B-4 dersi: serbest metin ayrıştırılmaz). Mini-spec'te `Design:` doluysa aynı biçim aranır.
   - "UI yok" kararı: `## User experience` başlığının altındaki yorum olmayan ilk satır
     `none — no user interface` ile başlıyorsa UI yok; başka her şey UI'dır (G-4).
   - Geçerli değer: `http://` ya da `https://` ile başlayan adres, ardından boş olmayan
     `(version <id>)`. Dosya yolu geçersizdir — tasarım repoya konmaz (G-1).
   - Kaçış yolu yoktur: `Design artifact: none — …` geçersizdir; kuraldan tek çıkış
     "none — no user interface"dır (G-2).
8. **Sürüm.** Yeni kural olduğu için `main`'de v1.25.0 (CHANGELOG, `.anew-version`); `lts/1.24`'e
   girmez (yalnızca düzeltme alır). CHANGELOG girdisi bir geçiş notu taşır: yükseltmeden önce
   `Approved`/`In progress` UI spec'lerine `Design artifact:` satırı eklenir, yoksa CI
   (`doctor --strict`) kırmızıya döner (G-3).

## User experience
none — no user interface

## Constraints & out of scope
- `specs/done/` taranmaz: daha önce ship edilmiş spec'ler bu kurala geriye dönük tabi değildir.
- `Draft` spec'te doctor sessizdir (artifact taslak sırasında üretilir).
- doctor adresin açılıp açılmadığını, artifact'ın gerçekten tüm ekranları kapsadığını ya da
  görselin doğruluğunu denetleyemez: kapsamı insan spec kapısında, karşılaştırmayı VERIFY yapar.
- doctor artifact'ın paylaşım ayarını denetleyemez (G-5); paylaşımı insan yapar.
- Split: değerlendirildi; tek yetenek, 9 kriter — tek spec (Selin Öztürk, 2026-11-12).
- Core'da araç adı yok (ANEW-0011); Cursor/Copilot/Codex/generic için araç önerisi bu işin dışında.
- Şablonun Türkçeleştirilmesi: 0007.

## Acceptance criteria
- [ ] AC-1 — `ANEW-0013` var (`Status: Accepted`), Requirement 1–4'ü kaydeder; ANEW-0008'de 0013'e
  işaret eden `Revised:` satırı var, 0008'in karar metni değişmemiş.
- [ ] AC-2 — `prompts/spec.md` ve `prompts/clarify.md` Requirement 2'yi söyler (ekran × durum
  kapsamı, kriter etiketi, var olan tasarıma bağlanma); "Never invent visuals" cümlesi kalmamış;
  core dosyalarında (`docs/`, `prompts/`, `workflows/`, `specs/`) hiçbir araç adı geçmez.
- [ ] AC-3 — `specs/TEMPLATE.md` ve `specs/TEMPLATE-mini.md` Requirement 3'teki satırı/biçimi taşır.
- [ ] AC-4 — `workflows/segments.md` ANALYZE kapısı Requirement 4'ü söyler (birlikte onay, eksikse
  onaya sunulmaz, onay sonrası değişiklik R-08).
- [ ] AC-5 — `prompts/verify.md` ve `docs/testing.md` Requirement 5'i söyler.
- [ ] AC-6 — Claude Code adapter'ı Requirement 6'yı söyler; `./scripts/doctor` "adapter parity" ok.
- [ ] AC-7 — selftest, doctor için şu durumları doğrular: (a) Approved + UI + alan yok → WARN,
  `--strict`'te FAIL; (b) Approved + UI + geçerli alan → mesaj yok; (c) Approved + UI + biçimi
  bozuk alan (adres http(s) değil / dosya yolu / `(version …)` yok ya da boş / `none — …`) →
  WARN, `--strict`'te FAIL; (d) "none — no user interface" → mesaj yok; (e) Draft + UI + alan yok
  → mesaj yok; (f) `specs/done/` içindeki alansız UI spec'i → mesaj yok; (g) mini-spec'te dolu
  ama biçimi bozuk `Design:` → WARN, `--strict`'te FAIL.
- [ ] AC-8 — CHANGELOG'da v1.25.0 girdisi Requirement 8'deki geçiş notunu taşır ve
  `.anew-version` = v1.25.0; selftest'in sürüm kontrolü yeşil.
- [ ] AC-9 — Dalın HEAD'inde CI `check` iş akışı başarılı (`doctor --strict` + `selftest`).

## Definition of Done
- [ ] Every acceptance criterion mapped to proof (test or reproducible observation)
- [ ] `scripts/check` green — bu repoda: HEAD'de CI `check` başarılı (`.anew-dev/README.md`, Doğrulama)
- [ ] Independent review done; real findings fixed, noise rejected with written rationale, deferred ones in `.anew-dev/backlog.md`
- [ ] Docs / ADRs updated if behavior or architecture changed
- [ ] Spec moved to `.anew-dev/done/` (it becomes immutable there)

## Scorecard (fill at ship — honest numbers make the process improvable)
| Metric | Value |
|---|---|
| Spec revisions | |
| Fix rounds | |
| Review findings: real / noise / deferred | |
| Regressions introduced | |
| Bugs escaped to production | |
