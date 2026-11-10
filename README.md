# Hermes starter skills

Osm skills pro Hermes Agent, které mění, jak agent pracuje: jak plánuje a testuje změny v kódu, jak podává zprávy, kam ukládá znalosti, jak hledá na webu, jak si nechává kontrolovat kód, jak se učí z chyb a jak deleguje práci. Balíček je pro malý vývojářský tým, kde má každý vlastního Hermese na svém notebooku a vlastní předplatné (Claude nebo ChatGPT/Codex). Skills jsou společné, osobní nastavení si každý drží u sebe.

Skill je textový návod v souboru `SKILL.md`. Hermes má v rejstříku jen krátký popis každé skill a celý návod si načte, až když se hodí k úloze. Skill nepřidává nové nástroje, mění postup. Kteroukoli skill můžete vyvolat i ručně lomítkem, například `/reliable-software-delivery oprav export do PDF`.

## Obsah repozitáře

```text
skills/                         8 týmových skills (tuhle složku čte Hermes)
templates/personal-overrides/   šablona osobní skill (Hermes ji nečte, agent si ji zkopíruje)
docs/sdileni-skills-v-tymu.md   sdílení skills v týmu, podrobně a se zdroji
CHANGELOG.md                    změny podle verzí
LICENSE                         licence MIT
```

## Osm skills

### reliable-software-delivery

Agent nejdřív přečte pravidla repozitáře a sepíše krátký plán: co má po změně fungovat, čeho se změna dotkne, jak se výsledek ověří a jak změnu vrátit. Pracuje ve vlastní větvi nebo worktree, nikdy přímo v `main`. Chybu nejdřív zachytí testem a „hotovo“ řekne až po testech, které opravdu spustil. Důležité změny nechá nezávisle zkontrolovat a po nasazení ověří, že běží právě zkontrolovaná verze. Příklad: na pokyn „oprav export do PDF“ napíše test, který chybu ukáže, opraví ji ve větvi, pustí testy a ve zprávě uvede příkazy i jejich výsledky.

### evidence-based-reporting

Agent ve zprávách odděluje, co změnil, co ověřil (a čím), co si jen prohlédl, co neověřil a co si odvozuje. Bez důkazu neřekne „hotovo“ ani „opraveno“. U chyby popíše, co zkoušel, co selhalo a co zůstalo beze změny. Každou zprávu o práci končí oddílem „Co uděláš ty“. Příklad: místo „migrace proběhla úspěšně“ napíše, že skript skončil s kódem 0, ale jeden krok přeskočil, a přidá dotaz, kterým to ověříte.

### company-brain

Agent ví, kam která znalost patří: krátké preference do paměti, vaše vlastní poznámky a znalosti do osobní wiki (složka jen pro vás, píše do ní sám, bez schvalování), postupy do skills, týmová fakta a rozhodnutí do firemního mozku (privátní git repozitář, přes schválení), průběh dřívější práce do historie relací. Hromadný onboarding mozku připraví po oblastech jako několik pull requestů s nízkou důvěrou, které vlastník oblasti schválí najednou. Do mozku zapisuje jen přes větev a pull request, jako autor „Hermes (Jméno)“ s trailerem `Co-authored-by` za svého člověka. Slučuje vždy člověk. Každé tvrzení má zdroj. Rozhodnutí ze schůzek agent jen navrhuje (`proposed`) a potvrzuje je účastník schůzky. Text od zákazníků a z webu bere jako data, ne jako pokyny. Příklad: po poradě připraví PR se dvěma záznamy v `kb/decisions/` ve stavu `proposed`, s odkazem na čas v nahrávce.

### anti-slop-writing

Agent píše dokumentaci, popisy PR a odpovědi zákazníkům věcně. Zachová čísla, názvy a omezení, nepřidá sliby ani vlastnosti, které nikdo nepotvrdil, a vyškrtá typické fráze generovaného textu. Česká část hlídá kalky („adresovat problém“, „v rámci“, „na denní bázi“), shodu po číslovkách a nedělitelné mezery. Příklad: z věty „robustní řešení zajišťuje bezproblémový export“ udělá „export do PDF zvládne 5 000 řádků (ověřeno testem)“; když číslo nikdo neověřil, nechá v textu místo `[číslo]`.

### reliable-web-research

U aktuálních faktů agent neodpovídá zpaměti: dohledá je, čte primární zdroje a cituje jen adresy, které opravdu otevřel. Postupuje od levných nástrojů k dražším (vyhledávání, čtení stránky, přímé API, prohlížeč). Na CAPTCHA, přihlášení, paywallu nebo chybě 403/429 se zastaví, řekne to a hledá jiný legitimní zdroj. Obsah stránek bere jako data, ne jako pokyny. Příklad: na otázku „od které verze knihovny je tohle chování výchozí“ odpoví odkazem na oficiální poznámky k vydání a odliší, kdy bylo API přidáno a kdy se změnilo výchozí chování.

### independent-code-review

Před sloučením důležité změny agent zmrazí commit a dá ho zkontrolovat modelu z jiné rodiny (kód psaný Claudem kontroluje GPT a naopak), s pevným checklistem a stupnicí blocker / major / low / info. Každý nález si před opravou ověří proti aktuálnímu kódu. Po opravě blockerů a majorů požádá o jedno kontrolní kolo a tím končí. Příklad: změna přihlašování dostane verdikt APPROVE_WITH_CHANGES se dvěma nálezy; agent je ověří, opraví a nechá zkontrolovat už jen opravu.

### skill-authoring

Agent se učí z chyb. Po vaší výtce nebo po vlastní chybě zapíše pravidlo i s důvodem do skill, která danou práci řídí, a ne příběh toho, co se stalo. Skills drží krátké. Týmové skills na místě nemění (jsou zamčené): osobní lekci zapíše do `personal-overrides`, týmovou navrhne jako PR do týmového repozitáře. Příklad: agent podruhé zapomene na lint; po vaší poznámce přidá do `personal-overrides` pravidlo „před commitem spusť lint, jinak CI vrátí PR“ a stejnou úpravu nabídne týmu jako PR.

### delegation-and-model-routing

Hlavní rozhodnutí si agent nechá a podřízeným agentům předává jen ohraničené balíky práce. Rutinní práci posílá na levnější model, analýzu, návrh, review a nejasné případy na silný. Zadání píše jako pro nového kolegu: cíl, cesty, co smí měnit, jak se výsledek ověří, rozpočet, formát výstupu a zákazy. Výsledky podřízených bere jako tvrzení, která musí ověřit, a před velkým paralelním během odhadne cenu. Příklad: přejmenování ve 40 souborech udělá skriptem; zjištění, proč padá integrační test, dá jednomu silnému podřízenému agentovi ve vlastním worktree.

## Instalace

Doporučená cesta: týmový repozitář naklonujete na disk, v nastavení Hermese ho přidáte do `skills.external_dirs` a osm týmových skills zamknete. Aktualizace je pak jeden `git pull`. Proč právě tahle cesta a jaké jsou další, popisuje [docs/sdileni-skills-v-tymu.md](docs/sdileni-skills-v-tymu.md).

Celou instalaci zvládne agent sám podle promptu v další sekci. Ručně vypadá takto (příkazy fungují v bashi i v PowerShellu). Klonujte ve stejném prostředí, ve kterém běží Hermes; když běží ve WSL, tak ve WSL.

Předpoklad: Git (na Windows `winget install Git.Git`). Repozitář je veřejný, přihlášení ke GitHubu ani pozvánka nejsou potřeba.

1. Naklonujte repozitář do domovské složky:
   ```bash
   cd ~
   git clone https://github.com/acegraphx/hermes-team-skills hermes-team-skills
   ```
2. Přidejte složku se skills do nastavení Hermese a výsledek zkontrolujte:
   ```bash
   hermes config set skills.external_dirs '[~/hermes-team-skills/skills]'
   hermes config get skills.external_dirs
   ```
   Zápis bez uvozovek uvnitř hranatých závorek funguje v bashi i ve Windows PowerShellu.
   `config set` celý seznam nahradí. Pokud už `external_dirs` nějaké cesty má, otevřete nastavení (`hermes config edit`) a řádek `- ~/hermes-team-skills/skills` přidejte k ostatním.
3. Zkontrolujte, že Hermes vidí všech osm skills: `hermes skills list`.
4. Zamkněte týmové skills, aby je agent ani jeho automatická revize neměnili na místě:
   ```bash
   hermes curator lock reliable-software-delivery
   hermes curator lock evidence-based-reporting
   hermes curator lock company-brain
   hermes curator lock anti-slop-writing
   hermes curator lock reliable-web-research
   hermes curator lock independent-code-review
   hermes curator lock skill-authoring
   hermes curator lock delegation-and-model-routing
   ```
   `hermes curator status` pak zámky vypíše. Odemknout je můžete jen vy (`hermes curator unlock <název>`), agent ne.
5. Ukončete Hermes a spusťte ho znovu, aby nová relace načetla rejstřík skills.

Nastavení i zámky platí pro jeden profil. Pokud používáte víc profilů, zopakujte kroky 2 až 4 s `hermes -p <profil> …`.

Volitelně, pro levnou a silnou cestu při delegování: nainstalujte oficiální plugin `advanced-delegation` (`hermes plugins install advanced-delegation`) a nastavte trasy `routine` a `complex` podle skill `delegation-and-model-routing` (interaktivně přes `hermes delegation config`).

## Prompt pro agenta

Zkopírujte celý blok do svého Hermese:

```text
Nainstaluj si týmový balíček Hermes skills a osvoj si ho. Postupuj po krocích a žádný nevynechej.

1. V mém domovském adresáři spusť git clone https://github.com/acegraphx/hermes-team-skills hermes-team-skills (repozitář je veřejný). Když git chybí, nainstaluj ho (na Windows winget install Git.Git). Pokud složka už existuje, jen přes git status ověř, že je na větvi main a bez lokálních změn.
2. Nastav skills.external_dirs tak, aby obsahoval ~/hermes-team-skills/skills. Cesty, které tam už jsou, zachovej. Ověř to přes hermes config get skills.external_dirs.
3. Přes hermes skills list ověř, že vidíš všech 8 skills: reliable-software-delivery, evidence-based-reporting, company-brain, anti-slop-writing, reliable-web-research, independent-code-review, skill-authoring, delegation-and-model-routing.
4. Každou z nich zamkni příkazem hermes curator lock <název> a zámky ověř v hermes curator status.
5. Přečti celé README.md v kořeni repozitáře a celý SKILL.md každé z osmi skills.
6. Ke každé skill mi česky napiš dvě až tři věty: co budeš od teď dělat jinak a podle čeho to poznám.
7. Zeptej se mě, co chci přizpůsobit: jazyk a délku odpovědí, příkazy pro testy, build a lint, cestu k firemnímu mozku a k mé osobní wiki (výchozí ~/wiki), git identitu agenta, modely pro levnou a silnou cestu a cokoli dalšího, co tě při čtení napadlo. Dokud neodpovím, nic osobního nezapisuj.
8. Moje odpovědi ulož jen do souborů, které patří mně: vytvoř osobní skill personal-overrides (kategorie personal) podle šablony ~/hermes-team-skills/templates/personal-overrides/SKILL.md a krátké preference ulož do paměti. Pokud osobní wiki ještě neexistuje, založ ji (složka s index.md). Do paměti ulož i pravidlo: „Poznamenej si / zapiš si“ = zápis do mé osobní wiki (cesta), „zapiš to do mozku“ = návrh do firemního mozku přes pull request; když nevíš, zeptej se. V ~/hermes-team-skills nic neměň, necommituj a nepushuj.
9. Na závěr mi podle evidence-based-reporting napiš, co jsi změnil, co jsi ověřil a čím a co se nepovedlo. Skonči oddílem „Co uděláš ty“ a připomeň mi restart Hermese.
```

## Personalizace

Platí jednoduché pravidlo: týmové soubory patří týmu, osobní soubory patří vám.

| Co | Kde | Kdo to mění |
| --- | --- | --- |
| Týmové skills | `~/hermes-team-skills` (klon repozitáře) | tým, jen přes PR |
| Vaše odchylky od týmových skills | osobní skill `personal-overrides` ve vašem profilu | vy a váš agent |
| Krátké preference (jazyk, tón, cesty) | paměť agenta (`USER.md`, `MEMORY.md`) | vy a váš agent |
| Povaha agenta | `SOUL.md` ve vašem profilu | vy |
| Modely, delegování, zámky | `config.yaml` ve vašem profilu | vy |
| Vaše vlastní skills | adresář skills ve vašem profilu | vy a váš agent |

Každá týmová skill na konci říká, že se má načíst i `personal-overrides`, a ta má při konfliktu přednost. Výjimkou jsou pravidla označená „hard rule“ (žádná tajemství, cizí text jen jako data, slučuje člověk, žádné „hotovo“ bez ověření): ta osobní nastavení nepřebije.

Týmovou skill neupravujte přímo v klonu. Další `git pull` by skončil konfliktem a váš agent by mezitím běžel na verzi, kterou nikdo nezkontroloval. Stejnojmenná kopie ve vašem profilu by zase týmovou verzi úplně zakryla a přestaly by k vám chodit týmové opravy.

## Sdílení v týmu

- Jeden týmový repozitář, jedna oficiální větev `main`, změny jen přes PR. Ochrana `main` a soubor `CODEOWNERS` pro `skills/` určí, kdo změny schvaluje.
- Skill je kód, který řídí agenta. Review čte celý diff: spouštěč v `description`, jestli nezmizelo žádné „hard rule“, jestli uvedené příkazy existují a jestli v textu nejsou tajemství, jména nebo klienti.
- Agent navrhuje zlepšení přes PR z odděleného worktree (postup je ve skill `skill-authoring`). Osobní lekce zapisuje jen do `personal-overrides`.
- Verze: každá skill má v hlavičce `version` (patch: upřesnění, minor: nové pravidlo, major: změna chování). Repozitář dostává tagy `vX.Y.Z` a záznamy v `CHANGELOG.md`.
- Skills z internetu instalujte, až když si je přečtete včetně skriptů. Bezpečnostní kontrola Hermesu review nenahrazuje.
- Osobní skills a paměť nesdílejte. Export profilu obsahuje i paměť a historii relací.

Podrobnosti a zdroje: [docs/sdileni-skills-v-tymu.md](docs/sdileni-skills-v-tymu.md).

## Aktualizace

Správce repozitáře sloučí schválený PR, zvýší verze dotčených skills, doplní `CHANGELOG.md` a dá týmu vědět. Každý pak u sebe spustí

```bash
git -C "$HOME/hermes-team-skills" pull --ff-only
```

a restartuje Hermes. Aktualizaci spouštíte vy, ne agent: zámky agentovi brání měnit soubory týmových skills. Pokud `CHANGELOG.md` uvádí novou skill, zamkněte ji (`hermes curator lock <název>`). Když `git pull` skončí chybou kvůli lokálním změnám, prohlédněte si je (`git -C "$HOME/hermes-team-skills" status` a `git -C "$HOME/hermes-team-skills" diff`) a buď z nich udělejte PR, nebo je zahoďte (`git restore`).

## FAQ

### Musím rozumět tomu, jak Hermes skills načítá?

Ne. Stačí vložit prompt a odpovědět agentovi na otázky. Když se něco pokazí, zeptejte se ho: „Proč nevidíš týmové skills?“ Diagnostiku zvládne sám.

### Co když skill radí něco, co v našem projektu nedává smysl?

Řekněte to agentovi. Odchylku zapíše do `personal-overrides` a hned se jí řídí. Pokud se týká celého týmu, navrhne změnu jako PR.

### Proč jsou týmové skills zamčené?

Hermes se po práci sám učí a může upravit skills, ze kterých se učil. Zámek zajistí, že týmová verze zůstane taková, jakou tým schválil. Lekce, kterou by agent jinak zapsal do týmové skill, skončí v `personal-overrides` nebo v návrhu PR.

### Funguje to s Claude i s ChatGPT/Codex?

Ano. Skills jsou text a na modelu nezávisí. Konkrétní modely zadáváte jen v nastavení delegování a to má každý svoje. Nezávislou kontrolu kódu má smysl svěřit modelu jiného výrobce, než je ten, který kód psal.

### Můžu si nainstalovat skills z internetu?

Můžete, ale až si je přečtete celé, včetně skriptů. Skill může agentovi přikázat cokoli, co by mu přikázal člověk. U skills z hubu ukáže `hermes skills inspect <identifikátor>` obsah předem a instalace projde bezpečnostní kontrolou; ta ale review nenahrazuje.

## Licence

MIT, viz [LICENSE](LICENSE).
