# Sdílení skills v týmu

Dokument vysvětluje, jak Hermes Agent instaluje a sdílí skills, proč balíček doporučuje klon repozitáře se `skills.external_dirs` a zámky a jak v malém týmu udržet společný základ a vedle něj osobní nastavení. Vychází z Hermes Agent v0.23.0 a z oficiální dokumentace (listopad 2026). Příkazy jsou ověřené přes `--help`. Nastavení `external_dirs`, výpis skills a zámek jsme vyzkoušeli v oddělené zkušební instalaci (vlastní `HERMES_HOME`): Hermes načetl všech osm skills a zámek u nich funguje.

## 1. Odkud Hermes skills načítá

- Hlavní adresář skills je `~/.hermes/skills/` v aktivním profilu. Hermes do něj kopíruje přibalené skills, instaluje tam skills z hubu a ukládá skills, které vytvoří agent. [1]
- Klíč `skills.external_dirs` v `config.yaml` přidá další adresáře. Cesty mohou obsahovat `~` a `${PROMĚNNÁ}`; neexistující cestu Hermes bez chyby přeskočí. [1]
- Repozitář projektu může nést vlastní skills v `.hermes/skills/` nebo `.agents/skills/`. Načtou se jen v relacích spuštěných uvnitř repozitáře, a to až po `hermes skills trust`. [1]
- Když se jména shodují, vyhrává pořadí projekt → `~/.hermes/skills/` → `skills.create_dir` → `external_dirs`. Skrytou kopii Hermes zaloguje jako zastíněnou. [1]
- Agent má v rejstříku jen popisy skills a celý text načte, až když ho potřebuje. Každá skill je zároveň příkaz `/název-skill`. [1]

## 2. Čtyři cesty sdílení

### a) Klon repozitáře a `skills.external_dirs` (doporučujeme)

```bash
cd ~
git clone https://github.com/acegraphx/hermes-team-skills hermes-team-skills
hermes config set skills.external_dirs '[~/hermes-team-skills/skills]'
hermes skills list
hermes curator lock <název>                          # pro každou z osmi skills
git -C "$HOME/hermes-team-skills" pull --ff-only     # aktualizace
```

Proč tahle cesta:

- Jedno nastavení přinese všechny týmové skills, i ty, které přibudou později. Aktualizace je jeden `git pull`.
- Privátní repozitář funguje s běžným přihlášením do gitu (SSH nebo `gh auth login`), žádný další token není potřeba.
- Historie, review i návrat k předchozí verzi jsou obyčejný git, který vývojáři znají.
- Curator skills z `external_dirs` nearchivuje ani neslučuje a `hermes curator adopt` je odmítne převzít. [1] [2]

Na co dát pozor:

- `external_dirs` nechrání soubory proti zápisu. Agent upravuje skill tam, kde ji najde, tedy i v klonu. [1] Automatická revize po práci může upravit kteroukoli skill, ze které se učila, včetně externích. [2] Proto týmové skills zamykáme (oddíl 3).
- Hlavní ochranou obsahu je review v týmovém repozitáři (oddíl 7).
- `hermes config set` seznam nahradí. Kdo už má v `external_dirs` jiné cesty, přidá řádek přes `hermes config edit`.

### b) Tap a instalace z hubu

```bash
hermes skills tap add <owner>/<repo>
hermes skills install <owner>/<repo>/<skill>
hermes skills check
hermes skills update
```

- Tap je repozitář na GitHubu se složkou `skills/`. Privátní repozitář vyžaduje `GITHUB_TOKEN` v `.env`; bez tokenu má GitHub API limit 60 požadavků za hodinu. [1]
- Každá instalace projde bezpečnostní kontrolou; nové tapy mají důvěru `community`. [1]
- `hermes skills update` přeskočí skills, které jste lokálně upravili, dokud nepoužijete `--force`. [1]
- Každou skill instalujete zvlášť a nové skills z repozitáře se samy neobjeví. Pro příspěvky zpět stejně potřebujete klon.
- Hodí se pro veřejné sdílení a pro jednotlivé skills; `hermes skills install https://…/SKILL.md` nainstaluje i jedinou skill z URL. [1]

### c) Distribuce profilu

```bash
hermes profile install <git-url> --alias
hermes profile update <název>
hermes profile info <název>
```

- Distribuce je git repozitář s celým agentem: `SOUL.md`, `config.yaml`, `skills/`, `cron/jobs.json`, `mcp.json` a `distribution.yaml`. [3]
- Při aktualizaci se nahradí soubory distribuce (distribution-owned): `SOUL.md`, `mcp.json`, `skills/`, `cron/jobs.json`, `distribution.yaml`; `config.yaml` se ve výchozím stavu zachová. Soubory uživatele (user-owned) zůstanou beze změny: `memories/`, `sessions/`, `state.db*`, `auth.json`, `.env`, `logs/`, `workspace/`, `plans/`, `home/`, `*_cache/`, `local/`. [3]
- Instaluje se jako nový profil, ne do výchozího; importovat profil jako `default` nejde. [3]
- Hodí se, když tým chce jednoho standardního agenta, třeba společného reviewera PR. Pro startovní balíček je to moc: lidem by přepsal povahu agenta i nastavení.

### d) Skills uvnitř repozitáře projektu

```bash
hermes skills trust            # spuštěno v kořeni repozitáře
```

- Skills v `.hermes/skills/` nebo `.agents/skills/` platí jen v relacích uvnitř toho repozitáře a mají nejvyšší přednost. [1]
- Hermes je před zařazením do rejstříku kontroluje a nebezpečné dá do karantény. Curator je nikdy nemění. [1]
- Hodí se pro pravidla jednoho projektu (build, nasazení, konvence). Startovní balíček nenahradí, ale dobře ho doplní.

## 3. Curator, automatická revize a zámky

- Curator spravuje jen skills, které vytvořila automatická revize (značka `created_by: agent`). Skills z hubu nikdy nemění. Ručně vytvořené skills a skills z `external_dirs` značku nemají a Curator je nechá být; `hermes curator adopt` odmítne skills přibalené, z hubu i externí. [2]
- Automatická revize po práci ale smí opravit kteroukoli skill, ze které se učila, včetně externích a z hubu. Každý takový zápis jde do záznamu změn (`hermes curator ledger`). [2]
- `hermes curator lock <skill>` tomu zabrání. Zamčená skill se dál čte a načítá, ale `skill_manage`, `write_file`, `patch` i `terminal` její změnu odmítnou, a to v běžných relacích, v cronu, u podřízených agentů, při automatické revizi i u Curatoru. Agent zámek zrušit nemůže; vaše vlastní úpravy v editoru fungují. Zámek platí pro jeden profil. [2]
- Přísnější varianta pro celý profil je `skills.write_approval: true`. Každý zápis do skills pak čeká na schválení (`/skills pending`, `/skills diff <id>`, `/skills approve <id>`). [1]
- Nechtěnou změnu vrátíte přes `hermes curator ledger --skill <název>` a `hermes curator rollback <id-záznamu>`. [2]

## 4. Co je týmové a co osobní

Rozdělení odpovídá tomu, jak dokumentace distribucí odlišuje soubory distribuce od souborů uživatele. [3]

| Vrstva | Kde | Kdo mění | Co s ní udělá aktualizace týmu |
| --- | --- | --- | --- |
| Týmové skills | klon `~/hermes-team-skills` | tým přes PR | `git pull` je nahradí |
| `personal-overrides` | adresář skills ve vašem profilu | vy a váš agent | nic |
| Paměť (`USER.md`, `MEMORY.md`) | váš profil | vy a váš agent | nic |
| `SOUL.md`, `config.yaml` | váš profil | vy | nic |
| Vaše další skills | adresář skills ve vašem profilu | vy a váš agent | nic |

- Každá týmová skill odkazuje na `personal-overrides`; ta má při konfliktu přednost, kromě pravidel označených „hard rule“.
- Stejnojmenná skill ve vašem profilu by týmovou úplně zastínila a přestaly by k vám chodit týmové opravy. Používejte ji jen vědomě a dočasně.

## 5. Jak agent navrhuje zlepšení

1. Lekci hned použije přes `personal-overrides`.
2. Řekne svému člověku, co by v týmové skill změnil a proč.
3. Po souhlasu vytvoří z klonu oddělený worktree (`git -C <klon> worktree add ../hermes-team-skills-pr-<téma> -b skill/<login>/<téma> origin/main`), úpravu udělá tam, zvýší `version`, doplní `CHANGELOG.md`, commitne jako agent s trailerem `Co-authored-by` a otevře PR přes `gh pr create`.
4. PR posoudí správce skills. Slučuje člověk, nikdy agent.
5. Po sloučení si každý stáhne novou verzi a agent smaže duplicitní pravidlo z `personal-overrides`.

Šablona popisu PR:

```text
Skill: <název>   verze: <stará> -> <nová>
Lekce: <jedno pravidlo v rozkazovacím způsobu>
Proč: <obecná chyba, které pravidlo zabrání; bez jmen a klientů>
Změna: <co přibylo, co ubylo>
Ověřeno: <úloha, na které jsme pravidlo zkusili; kladný i záporný případ>
```

## 6. Pojmenování a verze

- Názvy malými písmeny se spojovníky, stejné jako název složky, pojmenované podle druhu práce (`reliable-web-research`), bez jmen klientů a produktů.
- `description` má nejvýš 60 znaků a začíná spouštěčem („Use when …“). Rejstřík ukazuje agentovi jen tenhle řádek.
- Osobní skills pojmenujte tak, aby se nepletly s týmovými, například `personal-overrides` nebo s předponou `my-`.
- `version` v hlavičce každé skill: patch = upřesnění formulace nebo lekce, minor = nové pravidlo nebo oddíl, major = změněné nebo odebrané chování.
- Repozitář: tag `vX.Y.Z` pro každé vydání a záznam v `CHANGELOG.md`, včetně nových skills, které je potřeba zamknout.
- Když nová verze něco rozbije, vraťte se na předchozí tag: `git -C "$HOME/hermes-team-skills" switch --detach v1.0.0`. Zpět na aktuální stav: `git -C "$HOME/hermes-team-skills" switch main`.

## 7. Review změn skills

Skill je kód, který řídí agenta: může ho přimět spustit příkaz, něco odeslat nebo něco přeskočit. Změny proto procházejí stejným review jako kód.

- Soubor `CODEOWNERS` v `.github/`, v kořeni nebo v `docs/` určí, koho GitHub automaticky požádá o review; při povinném review lze vyžadovat i schválení vlastníka kódu. V privátních repozitářích to vyžaduje placený plán (Pro, Team, Enterprise). [4]
- Ochrana větve `main`: povinný pull request s review a povinné kontroly. Force push a smazání větve ochrana ve výchozím stavu zakazuje. [5]
- Příklad `CODEOWNERS`:

  ```text
  /skills/      @<org>/hermes-skill-maintainers
  /templates/   @<org>/hermes-skill-maintainers
  /.github/     @<org>/hermes-skill-maintainers
  ```

- Slučujte přes merge commit, ne squash, aby commity agenta zůstaly i s autorem a trailery.
- Co reviewer kontroluje: spouštěč v `description`; jestli nezmizelo pravidlo označené „hard rule“; jestli příkazy a klíče v textu existují (`--help`, dokumentace); délku skill; jestli v textu nejsou tajemství, osobní údaje nebo jména klientů; jestli nové pravidlo nezakazuje legitimní práci.
- Větší změnu vyzkoušejte v odděleném profilu, který čte skills z worktree PR: `hermes profile create skilltest --no-skills` a potom `hermes -p skilltest config set skills.external_dirs '[<cesta-k-worktree>/skills]'`. [1]

## 8. Skills od cizích lidí

- Neinstalujte skills, které nikdo z týmu nečetl. Skill může agentovi přikázat cokoli, co by mu přikázal člověk, včetně odeslání dat ven.
- Hermes proto nenačítá skills z cizích klonovaných repozitářů, dokud repozitáři nedáte důvěru přes `hermes skills trust`. [1]
- Instalace z hubu projde bezpečnostní kontrolou a `hermes skills inspect <identifikátor>` ukáže obsah předem. Kontrola hledá známé vzory, review nenahrazuje. [1]
- U distribucí platí totéž: `SOUL.md` a skills jsou aktivní od první zprávy, takže je přečtěte před prvním spuštěním. [3]

## 9. Soukromí osobních skills

- Osobní skills, paměť a `SOUL.md` zůstávají ve vašem profilu a do týmového repozitáře nepatří.
- Export profilu (`/export`, `hermes profile export`) obsahuje i `memories/`, `sessions/` a `USER.md` a jejich obsah nikdo neprověřuje. Neposílejte ho jen tak dál. [3]
- Distribuce paměť ani historii relací nikdy nepřenáší. [3]
- Tajemství do skills nepatří. Skill může deklarovat potřebné proměnné prostředí (`required_environment_variables`), hodnoty patří do `.env`. [1]

## Zdroje

1. Skills System, dokumentace Hermes Agent: https://hermes-agent.nousresearch.com/docs/user-guide/features/skills
2. Curator, dokumentace Hermes Agent: https://hermes-agent.nousresearch.com/docs/user-guide/features/curator
3. Profile Distributions, dokumentace Hermes Agent: https://hermes-agent.nousresearch.com/docs/user-guide/profile-distributions
4. About code owners, GitHub Docs: https://docs.github.com/en/repositories/managing-your-repositorys-settings-and-features/customizing-your-repository/about-code-owners
5. About protected branches, GitHub Docs: https://docs.github.com/en/repositories/configuring-branches-and-merges-in-your-repository/managing-protected-branches/about-protected-branches
6. Subagent Delegation, dokumentace Hermes Agent: https://hermes-agent.nousresearch.com/docs/user-guide/features/delegation

Příkazy ověřené přes `--help` v Hermes Agent v0.23.0: `hermes skills install | tap add | check | update | inspect | trust | list`, `hermes curator lock | unlock | status | ledger | rollback`, `hermes profile install | update | info | export`, `hermes config set | get | edit | path`, `hermes delegation config [--show]`, `hermes plugins install | enable | show`.
