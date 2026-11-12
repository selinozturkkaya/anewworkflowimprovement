# .anew-dev/

ANEW's own development records: the specs and plans that change the template itself.
They follow the same rules as a project's `specs/` (`specs/TEMPLATE.md`, `specs/plans/TEMPLATE.md`,
AGENTS.md invariant rules) but live here so the template's `specs/` stays empty for its users.

- `specs/NNNN-<name>.md` — one per piece of work; numbering is shared with `plans/`.
- `plans/NNNN-plan.md` — the approved plan, `Approved by / on` is the gate record.
- Shipped specs move to `done/` and are never edited again.
- `docs/testing.md` rule 6 (a ship commit touching only `specs/` inherits the full green) applies
  here to `.anew-dev/`: `git diff --name-only <green-sha> HEAD -- . ':!.anew-dev/'` prints nothing.

- doctor scans `specs/`, not `.anew-dev/`: ANEW's own ships are checked by the reviewer and
  VERIFY against the same rules (ticked or moved-out criteria, open targets), not by doctor. In rule 6
  doctor's judgment of the ship is replaced by this: before the ship commit, the human (or a
  reviewer on the ship diff) checks the `done/` file against those rules, with the diff shown;
  only then does the `.anew-dev/` ship commit inherit the full green.

## Releases and the LTS line

- Every release is an annotated tag on the commit that ships it (message: its CHANGELOG heading),
  pushed by name; its GitHub release carries the CHANGELOG entry.
- `lts/1.24` starts at the v1.24.1 tag and takes fixes only (`README.md`, Versions and support).
- A fix lands on `main` first, through its own spec. It reaches `lts/1.24` by
  `git cherry-pick -x <main commit>` in a PR with CI green. Its `.anew-dev/` hunks are dropped;
  its `CHANGELOG.md` and `.anew-version` hunks are replaced by the branch's own `v1.24.<n+1>`
  entry and marker. After merge the tag `v1.24.<n+1>`; then `main`'s entry for that fix gets
  "also in v1.24.<n+1>".
- If the fix's other files do not apply cleanly, it is redone on the branch, never forced. Every
  fix on `lts/1.24` names its `main` commit (`cherry picked from`; a redone fix writes that line
  by hand): no fix reaches the line that `main` does not already hold.

Template users: this folder is not part of your workspace — delete it after you create your repo.

## Bu çalışma kopyası — şirket içi ANEW iyileştirmesi

Karar (R-10, 2026-11-12, Selin Öztürk): bu repo ANEW yaşam döngüsünün kendisini iyileştirir;
ekipler iyileştirilmiş şablonu şirket içi yazılımlarında kullanır. Bu yüzden **bootstrap burada
uygulanmaz**: `AGENTS.md`, `docs/`, `specs/` ve `scripts/check.conf` kullanıcılar için şablon
olarak kalır. İyileştirmeler yukarıdaki kurallarla `.anew-dev/` altında yürür (spec numarası
`done/` ve `plans/` içindeki en yüksek numara + 1; sıradaki 0006). AGENTS.md değişmez kuralları
burada da aynen geçerlidir.

- Dil: chat=tr · docs=tr — `.anew-dev/` kayıtları (spec, plan, review, backlog girdileri) Türkçe;
  protokol alanları (`Status:`, `Approved by / on:`, `Source:`, şablon başlıkları) İngilizce.
  Şablonun kendi dosyaları (core: `docs/`, `workflows/`, `prompts/`, `AGENTS.md` vb.) **Türkçe
  olacak** (karar 2026-11-12, Selin Öztürk). Bu bir şablon değişikliğidir: kendi `.anew-dev`
  spec'iyle yapılır (önerilen ilk iş, 0006); o ship edilene kadar core İngilizce kalır. Protokol
  alanları ve `scripts/doctor`'un okuduğu başlıklar/değerler İngilizce kalır.
- Mod: **lite** (2026-11-12, Selin Öztürk) — spec onayı hızlı evet/hayır, plan onayı insanda;
  review bağımsız subagent ya da ayrı oturumda (temiz review VERIFY'ı taşıyabilir —
  `workflows/segments.md`, "The chainer"); triage ve ship insanda. `/new-feature` burada
  çalışmaz (AGENTS.md `Mode: unset` kalır; segment giriş kontrolleri `specs/`'e bakar): adımlar
  `.anew-dev/` üzerinde elle izlenir.
- Onay sahibi: **Selin Öztürk** (2026-11-12) — spec onayı, plan onayı, triage ve ship kapılarının
  tek insan onaylayanı. Onay hafif: kapıda "onay"/"evet" yeterli; ajan kayıt satırını
  (`Approved by / on: Selin Öztürk, <YYYY-MM-DD>` vb.) yazar. Ajan hiçbir kapıyı kendisi geçemez;
  bağımsız review/QA read-only `reviewer` subagent'ı ya da ayrı oturumdur (kural 3).
- Önemsiz (trivial) değişiklik politikası: **(1) PR + bir review, spec yok** (2026-11-12, Selin
  Öztürk) — `workflows/change-request.md` triage'ı TRIVIAL dediğinde (kriter değişmez, paylaşılan
  koda dokunulmaz): tek dar commit, `trivial/<kısa-ad>` dalı, review read-only `reviewer`
  subagent'ı, merge Selin Öztürk. Script davranışı ya da prompt anlamı değişiyorsa trivial
  değildir → `.anew-dev` spec'i.
- Doğrulama (2026-11-12, Selin Öztürk): `scripts/check.conf` yazılmaz (şablona girer).
  **Tam yeşil** = dalın HEAD'inde GitHub Actions `check` iş akışı başarılı (`doctor --strict` +
  `selftest`); kanıt planın `## Build evidence` bölümüne çalışma bağlantısı + SHA olarak yazılır.
  **İç döngü** = yerel `./scripts/doctor`, script değişince `./scripts/selftest`. Ortam (R-13):
  bu Windows makinesi selftest'i çalıştırabiliyor ama yavaş (>10 dk, 281/281 ok; CI ~1 dk) —
  tam profil kanıtı CI'dan gelir. Residue: `check.conf` adımı olmadığından RESIDUE NOT MEASURED;
  `scripts/check`/`scripts/residue` değiştiren iş bunu kendi VERIFY'ında selftest ile kanıtlar.
- Uzak repo / CI ve birleştirme (2026-11-12, Selin Öztürk): `origin` =
  `github.com/selinozturkkaya/anewworkflowimprovement`; CI = `.github/workflows/check.yml` (her
  push/PR). Her dal PR ile `main`'e **squash-merge**; merge öncesi CI yeşil, merge'ü Selin Öztürk
  yapar. `main` koruması (GitHub ayarı, Selin Öztürk yapar): PR zorunlu + `doctor` ve `check`
  status check'leri zorunlu; "Require approvals" kapalı (tek onaylayan). `gh` CLI isteğe bağlı —
  yoksa PR tarayıcıdan açılır.
