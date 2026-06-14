# Independent Penal Code Review

Review date: 2026-05-14

This review treats `ef-mdt-penal-code.json` as the source of truth and uses `penal-code-charge-degree-map.csv` for tags. It is gameplay design analysis informed by California relative punishment structure, not legal advice or a literal California sentencing chart.

## Playability Constraint: 120-Minute Prison Ceiling

The code's `maxTime` of 120 should be treated as a real playability budget, not merely a mathematical cap. Because months translate 1-to-1 into minutes, 120 months means two hours of prison roleplay after a large scene. That is already a major out-of-character and in-character consequence.

The revised recommendation is therefore not to raise time broadly. Time is the scarce punishment resource. It should be reserved for custody-worthy danger, violence, coercion, and institutional breakdown. Fines, restitution, forfeiture, licensing consequences, and narrowly targeted modifiers should carry more of the escalation for economic, regulatory, contraband, and property-heavy offenses.

Suggested base-time bands:

| Base Time | Suggested Use |
| ---: | --- |
| 0 | Citation-only, infractions, low-level possession/regulatory conduct |
| 1-10 | Ordinary misdemeanors and low-custody misdemeanors |
| 10-20 | Serious misdemeanors, low felonies, status felonies |
| 20-40 | Ordinary felonies, serious theft, robbery, carjacking, kidnapping, dangerous public-safety offenses |
| 40-60 | Serious violent felonies, homicide, major trafficking, jailbreak, major corruption |
| 60-80 | Top-tier ordinary single-charge conduct, such as first degree murder or extreme aggravated violence |
| 80-120 | Exceptional conduct, special-circumstance murder, mass-casualty danger, insurrection-level crisis |

No ordinary single charge should normally exceed 70-80 base time. The 120 ceiling should remain available for stacked charges, extreme aggravation, mass-casualty cases, or explicit exceptional offenses.

## Generated Range Table

I generated `penal-code-liability-sentencing-ranges.csv` with one row per charge degree and liability option. It contains 221 liability-specific rows.

Range methodology:

- `baseFine` and `baseTime` are the degree values after the liability option multipliers.
- `baseSeverity = baseTime * 10 + sqrt(baseFine)`.
- `minFine`, `minTime`, and `minSeverity` apply all eligible mitigating modifiers with defined effects, in JSON modifier order.
- `maxFine`, `maxTime`, and `maxSeverity` apply all eligible aggravating modifiers with defined effects, in JSON modifier order.
- Tiered modifiers are included for max-range calculations by applying the highest defined tier. For `prior-conviction`, that means `four-plus-priors`.
- Fine and time are clamped at zero for range calculations.

The min/max rows are intentionally mechanical. They are useful for stress testing, not a recommendation that every eligible modifier should stack in real cases.

## Sentencing Overview

### Classification Trends

| Classification | Rows | Avg Base Severity | Min Base Severity | Max Base Severity | Avg Base Time | Avg Base Fine | Avg Min Severity | Avg Max Severity |
| --- | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| Felony | 93 | 285.2 | 64.1 | 2541.4 | 24.3 | 2513 | 153.4 | 506.2 |
| Misdemeanor | 60 | 119.6 | 17.3 | 285.4 | 9.4 | 883 | 66.0 | 240.6 |
| Infraction | 35 | 16.0 | 10.0 | 31.6 | 0.0 | 293 | 10.9 | 29.4 |

The overall ladder works in the aggregate, but the overlap is broad. The highest misdemeanor, generic Impersonating at 285.4, is above several felony rows. The lowest felonies, including government-facility contraband and Class A weapon possession, sit in the 64-72 range.

### Charge Family Trends

| Family | Rows | Avg Base Severity | Max Base Severity | Avg Base Time | Avg Base Fine |
| --- | ---: | ---: | ---: | ---: | ---: |
| Offenses Against Public Safety | 22 | 304.9 | 2541.4 | 24.6 | 4548 |
| Offenses Involving Fraud | 8 | 286.7 | 509.2 | 25.0 | 1662 |
| Offenses Against Persons | 26 | 219.3 | 550.0 | 19.4 | 776 |
| Offenses Involving Theft | 29 | 214.3 | 700.0 | 17.5 | 2513 |
| Offenses Against Public Order | 19 | 164.1 | 444.7 | 14.5 | 435 |
| Offenses Involving the Well-Being of Wildlife | 13 | 163.3 | 513.2 | 12.7 | 1730 |
| Offenses Against Public Administration | 16 | 162.9 | 350.0 | 13.4 | 980 |
| Offenses Against Health and Morals | 23 | 153.4 | 670.7 | 12.3 | 1161 |
| Offenses Involving Damage to Property | 6 | 100.2 | 200.0 | 7.5 | 842 |
| Offenses Involving the Operation of a Vehicle | 26 | 52.5 | 238.7 | 3.3 | 479 |

Public safety is correctly high for a GTA RP code, but Insurrection and weapon sale/trafficking dominate the top of the scale. Fraud also runs high because it includes official impersonation and major economic offenses. Persons offenses contain the morally most serious conduct, but First Degree Murder at 550.0 is below several non-homicide rows.

With the 120-minute cap in mind, the solution is not simply to raise all serious person crimes until they outrank the high economic/public-safety rows. A better correction is selective: modestly raise underweighted violent/coercive crimes, reduce or fine-weight nonviolent outliers, and keep exceptional charges clearly marked as exceptional.

### Tag Trends

| Tag | Rows | Avg Base Severity | Max Base Severity | Avg Base Time | Avg Base Fine |
| --- | ---: | ---: | ---: | ---: | ---: |
| `organized-activity` | 1 | 2541.4 | 2541.4 | 240.0 | 20000 |
| `public-safety` | 22 | 304.9 | 2541.4 | 24.6 | 4548 |
| `fraud` | 8 | 286.7 | 509.2 | 25.0 | 1662 |
| `felony` | 93 | 285.2 | 2541.4 | 24.3 | 2513 |
| `explosive-related` | 8 | 276.4 | 709.5 | 21.2 | 4781 |
| `homicide` | 21 | 259.0 | 550.0 | 22.5 | 1283 |
| `public-order` | 24 | 251.5 | 2541.4 | 22.6 | 1331 |
| `government` | 52 | 237.3 | 2541.4 | 20.5 | 1629 |
| `person` | 26 | 219.3 | 550.0 | 19.4 | 776 |
| `violent` | 54 | 209.3 | 550.0 | 18.3 | 864 |
| `economic` | 52 | 200.4 | 709.5 | 16.2 | 2330 |
| `vehicle-related` | 50 | 130.8 | 700.0 | 10.3 | 1247 |
| `traffic` | 26 | 52.5 | 238.7 | 3.3 | 479 |
| `infraction` | 35 | 16.0 | 31.6 | 0.0 | 293 |

The high-level story is that institutional, public-safety, economic, and sale/transfer conduct often outranks person violence. That can be playable and intentional for DOJ roleplay, but it should be limited to conduct that really creates systemic or mass-risk danger.

Fine-heavy severity is appropriate for commercial and regulatory deterrence, but time-heavy severity should remain concentrated in violent, coercive, custody, and high-public-danger conduct.

## Major Findings

1. Intentional homicide is underweighted relative to non-homicide top-tier offenses. First Degree Murder is below Class D weapon sale, law-enforcement vehicle theft, drug trafficking, unlicensed import/export, and weapon trafficking.
2. Insurrection is outside the normal system. Its base time is 240, which exceeds the JSON sentencing guideline max of 120.
3. Torture, kidnapping, hostage taking, carjacking, and arson are too low compared with California's seriousness ladder, but the fix should be moderate time increases plus classification/degree cleanup, not sweeping time inflation.
4. Generic official/economic deception is high. Impersonating, Impersonating a Judge, Embezzlement, Illegal Fishing, and Possession of Government-Issued Items create misdemeanor/felony classification noise.
5. Several low-score felonies may be justified as status offenses, but they need explicit policy support: Class A weapon possession, ammunition possession, government-facility contraband, escaping, and restricted airspace.
6. Liability options are mostly useful, but conspiracy is uniformly discounted even though California often punishes felony conspiracy like the target felony, and murder conspiracy especially severely.
7. Tags need cleanup. Some rows appear to have tag bleed or overbroad tags, such as traffic infractions carrying `commercial`, `economic`, or `sale-transfer`, hunting infractions carrying `homicide` and `violent`, and unauthorized parking carrying `wildlife-animal`.
8. `penal-code-charge-degree-map.csv` is stale for First Degree Murder liability options. The CSV lists public-servant attempted/accessory options that are not present in the current JSON.
9. `prior-conviction` uses tiered effects rather than top-level effects. Any analysis tooling must read nested `tiers`, not only top-level `effects`.
10. `citation-discretion` is intended to be infraction-only by policy, but the current JSON also attaches it to 19 misdemeanor degrees. Decide whether those are intentional cite-and-release misdemeanors or remove the modifier from misdemeanor rows.
11. The most important balancing principle is time scarcity. High fines are a better tool than long prison time for nonviolent economic, commercial, regulatory, wildlife, licensing, import/export, and contraband-market conduct.

## Punishment Recommendations

| Charge / Degree | Current Base | Current Severity | Recommendation | Playability | California Alignment |
| --- | ---: | ---: | --- | --- | --- |
| First Degree Murder | 2500 fine / 50 time | 550.0 | Raise modestly to 60-70 time and 3500-5000 fine. Reserve 80-120 for special-circumstance murder, public-servant murder if kept separate, mass-casualty facts, or heavy charge stacking. | Keeps murder at the top of ordinary crimes without consuming the entire two-hour prison budget. | California treats first degree murder as life-tier, with special circumstances at the top of the ladder. |
| Second Degree Murder | 1750 / 40 | 441.8 | Raise modestly to 45-55 time and 2500-3500 fine. | Creates a clear gap between manslaughter and murder while leaving headroom for aggravators and stacked charges. | Second degree murder is still life-tier or near-life-tier relative to ordinary felonies. |
| Torture | 1500 / 20 | 238.7 | Raise to 35-45 time and 2500-4000 fine. | Gives investigators and prosecutors a meaningful alternative for prolonged cruelty without making it automatically eclipse murder. | California torture is a life-style felony and should sit near top-tier violence. |
| Kidnapping | 500 / 15 | 172.4 | Raise to 20-30 time and 1000-2000 fine. | Stops kidnapping from feeling like a low add-on to robberies and hostage scenes. | California kidnapping is a serious felony, especially with force, movement, ransom, or injury risk. |
| Hostage Taking | 750 / 20 | 227.4 | Raise to 30-40 time and 1500-3000 fine. | Rewards negotiation/rescue stakes without making every hostage scene murder-level. | Ransom/extortion kidnapping is punished very severely in California. |
| Carjacking | 400 / 20 | 220.0 | Raise to 25-35 time and 1000-2000 fine. | Better matches the danger of forced vehicle seizures in RP chases while leaving pursuit/injury charges room to matter. | California defines carjacking as force/fear vehicle taking and punishes it more severely than ordinary vehicle theft. |
| Robbery | 1000 / 25 | 281.6 | Keep 25-30 or raise to 30 if Grand Larceny remains at 30. | Keeps force/fear theft above pure property theft. | California robbery is a serious violent felony below homicide but above ordinary theft. |
| Grand Larceny | 1000 / 30 | 331.6 | Reduce to 20-25 unless reserved for very high-value theft. | Prevents nonviolent theft from outranking robbery. | Grand theft is serious property crime, but force/fear elevates robbery. |
| Theft of Law Enforcement Vehicle | 10000 / 60 | 700.0 | Reduce to 35-45 baseline, keep or raise fine, and use pursuit, weapon, injury, or public-safety charges for time escalation. | Keeps a strong deterrent without making property theft outrank murder by default. | California treats emergency/public-safety vehicle facts seriously, but ordinary vehicle theft does not outrank murder. |
| Theft of Aircraft | 5000 / 40 | 470.7 | Keep 30-40, below rebalanced homicide. | High enough for rare aircraft crime without distorting the whole theft family. | Aircraft theft is high-risk property/public-safety conduct, but still below intentional killing. |
| Operating Unlicensed Import/Export Business | 20000 / 50 | 641.4 | Split ordinary licensing violation from organized contraband import/export; ordinary should be 5-15 time and fine-heavy, organized should be 30-40 time with very high fine. | Lets DOJ punish large contraband rings without crushing business-paperwork mistakes. | California generally distinguishes licensing/regulatory violations from organized trafficking. |
| Drug Trafficking | 5000 / 60 | 670.7 | Keep serious at 45-60 time, but prefer fine/forfeiture escalation over raising time above 60. | Trafficking remains a major organized-crime driver without taking the full prison budget by itself. | Controlled-substance sale/transport is much more serious than possession, but not usually above murder. |
| Sale of Controlled Substance | 800 / 10 | 128.3 | Reclassify as felony 15-25 or add felony aggravated-sale tier. | Gives prosecutors a middle tier between possession and trafficking. | California sale/transport is felony-level, unlike simple possession. |
| Felony Drug Possession | 450-750 / 15 | 171.2-177.4 | Keep only if based on amount/prior/prohibited circumstance; otherwise reduce or clarify threshold. | Avoids overcharging simple possession while preserving bulk-possession enforcement. | California simple possession is usually misdemeanor-level unless aggravating prior/status rules apply. |
| Arson | 2500 / 15 | 200.0 | Split by target: property 15-20, structure/forest 20-30, inhabited/injury 30-40. Use fines for property loss. | Better handles small fires, building fires, and mass-risk fires separately. | California arson scales sharply by injury, inhabited structure, structure/forest, and property. |
| Criminal Sale of Weapon Class D | 12000 / 60 | 709.5 | Keep 50-60 unless mass-casualty explosives are built in. Increase fine rather than time for ordinary market aggravation. | Retains fear of heavy-weapon commerce without flattening homicide hierarchy. | Weapons/explosives trafficking is serious, but intentional murder remains a benchmark top crime. |
| Weapon Trafficking | 11000 / 45 | 554.9 | Keep 40-50, below murder and above ordinary sale/possession; use fines for network scale. | Strong organized-crime hook for firearm networks. | Sale/trafficking should outrank possession and ordinary regulatory crimes. |
| Illegal Manufacturing of Firearms | 5000 / 10 | 170.7 | Raise to 15-25 and increase fine if needed. | Makes manufacturing feel closer to trafficking than possession without overusing prison time. | Manufacturing illegal firearms is more serious than mere possession. |
| Criminal Use of Weapon | 4000 / 15 | 213.2 | Reclassify as felony 20-25, or narrow misdemeanor to non-injury brandishing/use. | Avoids a high-score misdemeanor that reads like a felony in practice. | Active use of a weapon during crime is more serious than possession or brandishing. |
| Impersonating | 1250 / 25 | 285.4 | Reduce generic impersonation to 10-15; reserve 25+ and higher fines for official, financial, or custody-impacting impersonation. | Keeps identity RP punishable without eclipsing violence. | California-style hierarchy treats official impersonation more seriously than ordinary false identity. |
| Impersonating a Judge | 3500 / 45 | 509.2 | Reduce to 25-35 unless warrants, detention orders, or major case corruption are involved; use fines and public-office consequences for escalation. | Still severe, but not near human murder. | Judicial impersonation is serious institutional harm, but not usually homicide-adjacent absent coercive custody consequences. |
| Embezzlement | 1000 / 20 | 231.6 | Split by value/trust role; ordinary misdemeanor 10-15, high-value/public-office felony 20-30. | Lets prosecutors charge workplace theft without making every case a major felony. | Theft by breach of trust scales heavily by value and role. |
| Violation of Restraining Order | 525 / 20 | 222.9 | Ordinary violation 10-15; threats, stalking, violence, or repeat conduct should be felony/wobbler. | Better fits common enforcement scenes and leaves room for aggravated conduct. | California distinguishes violation conduct, threats, violence, and repeat behavior. |
| Harboring a Fugitive | 375 / 20 | 219.4 | Split by fugitive seriousness or reduce ordinary cases to 10-15. | Avoids punishing minor concealment like a violent felony. | Accessory/harboring liability depends heavily on the underlying felony and intent. |
| Illegal Fishing | 6250 / 15 | 229.1 | Reduce ordinary illegal fishing time or split commercial/protected-species version; keep wildlife deterrence primarily fine-heavy. | Maintains conservation enforcement without odd violent-crime comparisons. | Wildlife penalties are often fine-heavy and aggravated by commercial/protected conduct. |
| Murder of Police Working Dog | 4000 / 45 | 513.2 | Reduce to 30-35, or keep only if explicitly a special public-safety felony. | Protects K9 RP without outranking human second degree murder after rebalancing. | Killing a working animal is serious, but California's human homicide ladder should remain dominant. |
| Insurrection | 20000 / 240 | 2541.4 | Either mark as exceptional outside normal sentencing or cap at 120 time. | Prevents a single charge from breaking the sentencing-guideline ceiling. | State-crisis offenses can be exceptional, but the code should document the exception. |

## Modifier Recommendations

| Modifier Issue | Recommendation | Playability | California Alignment |
| --- | --- | --- | --- |
| `prior-conviction` is tiered | Keep the tiered model. The current shape is good for playability: 1 prior adds fine only, 2-3 priors adds modest fine plus time x1.1, and 4+ priors adds larger fine plus time x1.25. Update tooling and documentation to read `tiers`. | Escalates repeated conduct without making one prior conviction automatically inflate prison time. | Recidivism commonly affects charging, bail, probation, and sentencing exposure. |
| `prior-conviction` and `first-offense` can both be eligible | Enforce mutual exclusivity in UI or sentencing logic. | Prevents contradictory stacking. | A first offense and prior conviction are legally incompatible for the same defendant/context. |
| `citation-discretion` policy mismatch | If policy is infraction-only, remove it from the 19 misdemeanor degrees currently carrying it. If misdemeanor cite-and-release is intended, rename it to `cite-and-release-discretion` and add clear eligibility notes. | Prevents officers from seeing a citation modifier on misdemeanor charges where custody may still be expected. | Citation treatment usually avoids custody on the charged offense, but some misdemeanors may still be cited under policy. |
| `minor-injury` on threat-only or no-contact charges | Remove from Criminal Threats and similar no-injury offenses; keep for assault, battery, traffic, animal, and weapon-risk offenses where injury is possible. | Avoids confusing "no lasting injury" on conduct that has no physical injury element. | Injury mitigation belongs to physical-force or accident offenses, not pure communication offenses. |
| `public-servant-victim` on non-victim or property offenses | Rename to `public-servant-target` or apply only when a public servant is personally victimized or targeted. | Reduces argument over property/government process cases. | California enhancements and special rules usually depend on victim status, duty status, knowledge, or official target. |
| `weapon-used` on Aggravated Assault | Consider removing if the degree already requires a weapon/dangerous-force basis, or keep only when a weapon is optional and materially aggravating. The new `firearm-discharged` modifier gives a cleaner escalation path for actual gunfire. | Prevents double-counting the same weapon fact. | Sentencing enhancements should not duplicate an element unless intentionally designed that way. |
| `medical-aid-rendered` on homicide | Keep only where aid plausibly mitigates culpability before death, or rename to `aid-rendered-after-injury`. | Rewards RP triage without making completed homicide look too lenient. | Aid after injury can matter, but completed homicide severity mainly turns on mental state and causation. |
| `organized-activity` on ordinary traffic/regulatory rows | Review and remove from minor infractions unless coordinated enterprise, gang, crew, or planned criminal conduct is a real scenario. | Keeps modifier menus tidy during routine stops. | Organization or gang-purpose facts should aggravate crimes where coordinated criminal purpose matters. |
| combined organized/gang modifier | Keep the merged definition broad enough to cover coordinated enterprise/planning and gang-associated benefit, direction, or material connection facts, while avoiding duplicate stacking. | Lets prosecutors handle smuggling rings, crews, and gang-related conduct under one enhancement. | California gang enhancement law is narrower than generic organized crime, so reports should document benefit, direction, association, or specific criminal purpose when gang facts are used. |
| `high-value-loss` on fixed high-value charges | Avoid stacking on charges already defined by high value unless there is a higher value threshold. | Prevents runaway fines for the same value fact. | Value thresholds generally define a degree or enhance above the threshold, not both without a separate tier. |

### Implemented Modifier Additions

Three California-style modifiers have been added to `ef-mdt-penal-code.json` and applied narrowly:

| Modifier | Effect | Current Application |
| --- | --- | --- |
| `firearm-discharged` | Fine x1.25 and time +10 | Assault, Aggravated Assault, Second Degree Murder, First Degree Murder, Kidnapping, Hostage Taking, Reckless Endangerment, Carjacking, Robbery, Criminal Use of Weapon |
| `on-release` | Fine +500 and time +5 | Aggravated Assault, Second Degree Murder, First Degree Murder, Kidnapping, Hostage Taking, Carjacking, Robbery, Arson, Drug Trafficking, Class D weapon sale, Criminal Use of Explosives, DUI Narcotics, Jailbreak, Reckless Evading, Weapon Trafficking |
| `occupied-structure-or-vehicle` | Fine x1.15 and time +5 | Reckless Endangerment, Burglary, Arson, Vandalism of Government Property, Criminal Use of Weapon, Criminal Use of Explosives |

The effects use fixed time additions rather than broad time multipliers so they stay predictable under the 120-minute cap.

### Potential Modifier Additions

These would fit California-style escalation and GTA RP gameplay. They should be applied narrowly, with anti-double-counting rules when the fact is already an element of the charge.

| Proposed Modifier | Suggested Effect | Best Fits | Why It Helps |
| --- | --- | --- | --- |
| `great-bodily-injury` | Time +5 to +10, fine x1.1-1.25 | Battery, assault, robbery, carjacking, arson, reckless evading, DUI, animal cruelty where serious injury is possible | California has a dedicated great bodily injury enhancement. In RP terms, this distinguishes bruises/minor wounds from Stage 1 incapacitation, coma-like injury, permanent impairment, or severe trauma. |
| `firearm-personally-used` | Time +5 to +10, fine x1.15; mutually exclusive with `weapon-used` or treated as a higher tier | Robbery, carjacking, kidnapping, assault, threats, burglary, drug trafficking | California distinguishes being armed from personally using a firearm. A separate firearm-use tier lets knives/blunt weapons stay under firearms without creating many new charges. |
| `vulnerable-victim` | Time +5 or fine x1.25, depending on offense | Assault, battery, theft/fraud, kidnapping, robbery, elder/dependent adult abuse analogues | California separately treats elder/dependent adult victimization as serious. This can cover elderly, disabled, medically helpless, or otherwise vulnerable victims without adding many standalone crimes. |
| `hate-motivated` | Fine x1.25 and time +5, limited to person/property/public-order crimes | Assault, battery, threats, vandalism, harassment, arson, robbery | California has hate-crime enhancements. It adds motive-based seriousness while keeping the base offense intact. |
| `large-scale-loss` | Fine +2500 to +10000, little or no time | Fraud, embezzlement, money laundering, smuggling, import/export, theft | California has an aggravated white-collar-crime enhancement. This is a better fine-heavy tool than raising prison time for nonviolent economic scale. |
| `school-or-sensitive-location` | Fine x1.25 or time +5, charge-limited | Weapons, drug sale, threats, reckless endangerment, public safety | California often treats sensitive places differently. Use sparingly for schools, hospitals, courthouses, jails, police stations, and critical infrastructure. |

Lowest-friction remaining additions: `great-bodily-injury`, `firearm-personally-used` or a tiered replacement for `weapon-used`, `hate-motivated`, and `large-scale-loss`.

## Liability Option Recommendations

| Liability Pattern | Recommendation | Playability | California Alignment |
| --- | --- | --- | --- |
| Attempt options are generally clear | Keep attempt as liability option for murder, kidnapping, robbery, jailbreak, aircraft theft, law-enforcement vehicle theft, and police working dog murder. | Reduces duplicated charge clutter while preserving inchoate conduct. | Attempt is a recognized liability theory and often punished at a fraction of the completed offense. |
| Conspiracy multiplier is uniformly x0.5 | Consider family-specific conspiracy multipliers. For violent/top-tier felonies, time x0.6-0.75 plus stronger fines is usually more playable than time x1.0. Reserve near-principal time for murder conspiracy, mass-casualty plans, or completed overt acts. | Conspiracy scenes become meaningful even when the plan is interrupted early, without turning every interrupted plan into a near-max prison stay. | California felony conspiracy is often punished like the target felony; murder conspiracy is especially severe. |
| Accessory options are attached to many violent felonies | Keep, but distinguish accessory-after-the-fact from aider/abettor. Aiding before/during should often be principal liability. | Helps officers/prosecutors avoid undercharging active participants. | California principals include aiders and abettors; accessory after the fact is a separate, lesser theory. |
| Robbery has both armed attempted/accessory labels and `weapon-used` modifier | Consider keeping generic attempted/accessory robbery only, with `weapon-used` as the armed fact. | Prevents duplicate mental model of armed robbery as both liability and modifier. | Weapon facts generally enhance/define aggravated conduct; attempt/accessory speaks to participation stage. |
| First Degree Murder liability mismatch between JSON and CSV | Regenerate or patch `penal-code-charge-degree-map.csv` so it matches JSON. If public-servant attempted/accessory options are desired, add them back to JSON deliberately. | Prevents developers from balancing against stale rows. | Data consistency matters more than which policy is selected. |
| Murder of public servant | Prefer a separate degree or special liability option only if the server wants it to exceed ordinary first degree murder. | A visible top-tier charge is easier for DOJ players to reason about than hidden multipliers. | California treats peace officer murder as special-circumstance murder when statutory facts are met. |

## Classification Recommendations

| Charge / Degree | Current Classification | Recommendation |
| --- | --- | --- |
| Criminal Use of Weapon | Misdemeanor | Reclassify as felony unless narrowed to non-injury threatening/display conduct. |
| Generic Impersonating | Misdemeanor | Reduce punishment or split into ordinary misdemeanor and official/financial felony tiers. |
| Embezzlement | Misdemeanor | Make value/trust-role tiers; high-value or public-office cases should be felony/wobbler. |
| Violation of Restraining Order | Misdemeanor | Keep ordinary misdemeanor; add aggravated felony/wobbler for threats, stalking, violence, or repeat conduct. |
| Harboring a Fugitive | Misdemeanor | Split by underlying fugitive seriousness; violent-felony harboring can be felony/wobbler. |
| Sale of Controlled Substance | Misdemeanor | Reclassify as felony or create an aggravated felony sale tier. |
| Criminal Possession of Weapon Class A | Felony | Consider misdemeanor unless prohibited person, restricted location, criminal-use facts, or repeated conduct are present. |
| Criminal Possession of Ammunition | Felony | Consider misdemeanor unless tied to prohibited person, restricted place, or weapons-trafficking facts. |
| Possession of Contraband in Government Facility | Felony | Keep felony for weapons, escape tools, or controlled substances; split lesser contraband to misdemeanor. |
| Unlawful Production of Moonshine | Felony | Consider misdemeanor unless commercial, hazardous, repeat, or injury-linked. |

## Additional Data Cleanup

- Keep regenerating `penal-code-charge-degree-map.csv` from JSON whenever modifier or liability options change. As of this pass, the CSV modifier and liability columns match the current JSON.
- Audit tags for false positives, especially `commercial`, `economic`, and `sale-transfer` on traffic infractions; `homicide` and `violent` on routine hunting violations; and `wildlife-animal` on unauthorized parking.
- Decide whether `baseTime` may exceed `sentencingGuidelines.maxTime`. If yes, add an explicit exception field for Insurrection.
- Consider adding modifier conflict metadata, such as `mutuallyExclusiveWith`, for `first-offense` vs `prior-conviction`.
- Consider adding degree threshold notes for value/quantity-driven crimes so officers know when to pick petty theft, grand theft, grand larceny, felony drug possession, cultivation tiers, and trafficking.

## California Calibration Sources

Primary California sources checked:

- [California Penal Code section 190](https://leginfo.legislature.ca.gov/faces/codes_displaySection.xhtml?lawCode=PEN&sectionNum=190): first and second degree murder punishment.
- [California Penal Code section 190.2](https://leginfo.legislature.ca.gov/faces/codes_displaySection.xhtml?lawCode=PEN&sectionNum=190.2): first degree murder special circumstances, including peace officer victim circumstances.
- [California Penal Code section 206](https://leginfo.legislature.ca.gov/faces/codes_displaySection.xhtml?lawCode=PEN&sectionNum=206): torture definition.
- [California Penal Code section 209](https://leginfo.legislature.ca.gov/faces/codes_displaySection.xhtml?lawCode=PEN&sectionNum=209): kidnapping for ransom/extortion and aggravated kidnapping punishment.
- [California Penal Code section 215](https://leginfo.legislature.ca.gov/faces/codes_displaySection.xhtml?lawCode=PEN&sectionNum=215): carjacking definition and punishment.
- [California Penal Code section 664](https://leginfo.legislature.ca.gov/faces/codes_displaySection.xhtml?lawCode=PEN&sectionNum=664): attempt punishment.
- [California Penal Code section 182](https://leginfo.legislature.ca.gov/faces/codes_displaySection.xhtml?lawCode=PEN&sectionNum=182): conspiracy punishment.
- [California Penal Code section 32](https://leginfo.legislature.ca.gov/faces/codes_displaySection.xhtml?lawCode=PEN&sectionNum=32): accessory after the fact.
- [California Penal Code section 451](https://leginfo.legislature.ca.gov/faces/codes_displaySection.xhtml?lawCode=PEN&sectionNum=451): arson tiers.
- [California Penal Code section 12022.7](https://leginfo.legislature.ca.gov/faces/codes_displaySection.xhtml?lawCode=PEN&sectionNum=12022.7): great bodily injury enhancement.
- [California Penal Code section 12022.5](https://leginfo.legislature.ca.gov/faces/codes_displaySection.xhtml?lawCode=PEN&sectionNum=12022.5) and [section 12022.53](https://leginfo.legislature.ca.gov/faces/codes_displaySection.xhtml?lawCode=PEN&sectionNum=12022.53): firearm use and discharge enhancements.
- [California Penal Code section 186.22](https://leginfo.legislature.ca.gov/faces/codes_displaySection.xhtml?lawCode=PEN&sectionNum=186.22): criminal street gang enhancement concepts.
- [California Penal Code section 422.75](https://leginfo.legislature.ca.gov/faces/codes_displaySection.xhtml?lawCode=PEN&sectionNum=422.75): hate-crime enhancement.
- [California Penal Code section 12022.1](https://leginfo.legislature.ca.gov/faces/codes_displaySection.xhtml?lawCode=PEN&sectionNum=12022.1): felony conduct while released on bail or own recognizance.
- [California Penal Code section 186.11](https://leginfo.legislature.ca.gov/faces/codes_displaySection.xhtml?lawCode=PEN&sectionNum=186.11): aggravated white-collar crime enhancement.
- [California Penal Code section 368](https://leginfo.legislature.ca.gov/faces/codes_displaySection.xhtml?lawCode=PEN&sectionNum=368): elder and dependent adult victimization.
- [California Health and Safety Code section 11350](https://leginfo.legislature.ca.gov/faces/codes_displaySection.xhtml?lawCode=HSC&sectionNum=11350) and [section 11352](https://leginfo.legislature.ca.gov/faces/codes_displaySection.xhtml?lawCode=HSC&sectionNum=11352): controlled-substance possession versus sale/transport.
- [California Vehicle Code section 23152](https://leginfo.legislature.ca.gov/faces/codes_displaySection.xhtml?lawCode=VEH&sectionNum=23152) and [section 2800.2](https://leginfo.legislature.ca.gov/faces/codes_displaySection.xhtml?lawCode=VEH&sectionNum=2800.2): DUI and reckless evading.
