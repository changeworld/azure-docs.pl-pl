---
title: Zestawy fonetyczne mowy - usługa mowy
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak za pomocą usługi mowy alfabetu fonetycznego mapuje się na międzynarodowy alfabet fonetyczny (IPA) i kiedy używać tego zestawu.
services: cognitive-services
author: zhaoyunED
manager: junwg
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: jiajzhan
ms.openlocfilehash: 770e97ad126f66efb43bf8cf7eb12f7510858192
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78675341"
---
# <a name="speech-service-phonetic-sets"></a>Zestawy fonetyczne usługi mowy

Usługa Mowy definiuje alfabety fonetyczne (w skrócie"zestawy telefonów"), składające się z siedmiu języków; `en-US`, `fr-FR` `de-DE`, `es-ES` `ja-JP`, `zh-CN`, `zh-TW`, i . Zestawy telefonów usługi mowy są zazwyczaj mapowane na <a href="https://en.wikipedia.org/wiki/International_Phonetic_Alphabet" target="_blank">Międzynarodowy Alfabet <span class="docon docon-navigate-external x-hidden-focus"> </span>Fonetyczny (IPA). </a> Zestawy telefonów usługi mowy są używane w połączeniu z [językiem znaczników syntezy mowy (SSML)](speech-synthesis-markup.md)jako część oferty usługi zamiany tekstu na mowę. W tym artykule dowiesz się, jak te zestawy telefonów są mapowane i kiedy używać zestawu telefonów.

# <a name="en-us"></a>[pl-Usa](#tab/en-US)

### <a name="english-suprasegmentals"></a>angielski suprasegmentals

| Przykład 1 (Początek spółgłoski, początkowe słowo dla samogłoski) | Przykład 2 (Intervocalic dla spółgłoski, słowo jądro przyśrodkowe dla samogłoski) | Przykład 3 (Coda dla spółgłoski, słowo końcowe dla samogłoski) | Komentarze |
|--|--|--|--|
| burger /b er **1** r - g ax r/ | falafel /f ax - l aa **1** - f ax l/ | gitara /g ih - t aa **1** r/ | Zestaw telefonów do obsługi mowy położył nacisk po samogłosce zestresowanej sylaby |
| inopportune /ih **2** - n aa - p ax r - t uw 1 n/ | niepodobne /d ih - s ih **2**- m ax - l eh 1 - r ax - t iy/ | pracownicy /w er 1 r k - f ao **2** r s/ | Zestaw telefonów do obsługi mowy położył nacisk po samogłosce sylaby podstresowej |

### <a name="english-vowels"></a>Samogłoski w języku angielskim

| `sapi` | `ipa` | Przykład 1     | Przykład 2 | Przykład 3                   |
|--------|-------|---------------|-----------|-----------------------------|
| Iy     | `i`   | **ea**t       | f**ee**l  | vall**ey**                  |
| Ih     | `ɪ`   | **i**f        | f**i**ll  |                             |
| Ey     | `eɪ`  | **a**te       | g**a**te  | d**ay**                     |
| Eh     | `ɛ`   | **e**bardzo     | p**e**t   | m**eh** (rzadkie słowo w końcu) |
| Ae     | `æ`   | **a**ctive    | c**a**t   | n**ah** (rzadkie słowo w końcu) |
| aa     | `ɑ`   | **o**bstinate | p**o**ppy | r**ah** (rzadkie słowo wreszcie) |
| ao     | `ɔ`   | **o**zasięg    | c**au**se | Ut**ah**                    |
| Uh     | `ʊ`   | b**oo**k      |           |                             |
| Ow     | `oʊ`  | **o**ld       | cl**o ne** | g**o**                      |
| Uw     | `u`   | U ber **(u**ber)      | b**oo**st | t**oo**                     |
| Ah     | `ʌ`   | **u**ncle     | c**u**t   |                             |
| Ay     | `aɪ`  | **i**ce       | b**i**te  | fl**y**                     |
| Awh     | `aʊ`  | **ou**t       | s**ou**th | c**ow**                     |
| Oy     | `ɔɪ`  | **oi**l       | j**oi**n  | t**oy**                     |
| y uw   | `ju`  | Yu ma **(yu)**      | h**u**człowiek | f**ew**                     |
| Ax     | `ə`   | **a**go       | wom**a**n | są**a**                    |

### <a name="english-r-colored-vowels"></a>Samogłoski w kolorze R w języku angielskim

| `sapi` | `ipa` | Przykład 1    | Przykład 2      | Przykład 3  |
|--------|-------|--------------|----------------|------------|
| ih r   | `ɪɹ`  | **ucho**s     | t**ir**amisu   | n**ucho**   |
| eh r   | `ɛɹ`  | samolot **powietrzny** | aplikacja**ar**ently | sc**ar**e  |
| uh r   | `ʊɹ`  |              |                | c**twój**e   |
| ay r   | `aɪɹ` | **Ire**ziemia  | f**ir**eplace  | ch**oir**  |
| aw r   | `aʊɹ` | **godz.**    | p**ower**ful   | s**nasze**   |
| ao r   | `ɔɹ`  | **lub**ange   | m**lub**al.      | s**wiosła**   |
| aa r   | `ɑɹ`  | **ar**tist   | st**ar**t      | c**ar**    |
| er r   | `ɝ`   | **ucho**th    | b**ir**d       | f**twój**    |
| ax r   | `ɚ`   |              | wszystko**er**gy    | **kolacja** |

### <a name="english-semivowels"></a>angielski Semivowels

| `sapi` | `ipa` | Przykład 1           | Przykład 2  | Przykład 3 |
|--------|-------|---------------------|------------|-----------|
| w      | `w`   | **w**ith, s**ue**de | al**w**ays |           |
| t      | `j`   | **y**ard, f**e**w   | na**i**na  |           |

### <a name="english-aspirated-oral-stops"></a>Angielski wolnossący ustny zatrzymuje

| `sapi` | `ipa` | Przykład 1 | Przykład 2   | Przykład 3  |
|--------|-------|-----------|-------------|------------|
| p      | `p`   | **p**ut   | ha**pp**en  | fla**p**   |
| b      | `b`   | **b**ig   | num**b**er  | cra**b**   |
| t      | `t`   | **t**alk  | capi**t**al | sough**t** |
| d      | `d`   | **d**ig   | prowadził**d**om  | ro**d**    |
| k      | `k`   | **c**ut   | sla**ck**er | Ira**q**   |
| g      | `g`   | **g**o    | a**g**o     | dra**g**   |

### <a name="english-nasal-stops"></a>Angielski Nosowe przystanki

| `sapi` | `ipa` | Przykład 1        | Przykład 2  | Przykład 3   |
|--------|-------|------------------|------------|-------------|
| m      | `m`   | **m**w, rozbić   | ca**m**era | roo**m**    |
| n      | `n`   | **n**o, s**n**ow | te**n**t   | chicke**n** |
| Ng     | `ŋ`   |                  | li**n**k   | **s ing**    |

### <a name="english-fricatives"></a>Angielski fricatives

| `sapi` | `ipa` | Przykład 1   | Przykład 2        | Przykład 3  |
|--------|-------|-------------|------------------|------------|
| k      | `f`   | **f**ork    | le**f**t         | hal**f**   |
| v      | `v`   | **v**alue   | e**v**ent        | lo**v**e   |
| Th     | `θ`   | **w 2015 roku**    | empa**th**y      | mon**th**  |
| Dh     | `ð`   | **th**pl    | mo**th**er       | smoo**th** |
| s      | `s`   | **s**to     | ri**s**k         | fakt**s**  |
| z      | `z`   | **z**ap     | bu**s**y         | dziecko**s**   |
| Sh     | `ʃ`   | **sh** e    | abbrevia**ti**na | ru**sh**   |
| Zh     | `ʒ`   | **J**acques (właśc. | zarzut**s**ure     | gara**g**e |
| h      | `h`   | **h**elp    | pl**ance**      | a-**h**a!  |

### <a name="english-affricates"></a>Angielskie affricates

| `sapi` | `ipa` | Przykład 1 | Przykład 2    | Przykład 3  |
|--------|-------|-----------|--------------|------------|
| ch     | `tʃ`  | **w ch**  | fu**t**ure   | atta**ch** |
| Jh     | `dʒ`  | **j**oy   | ori**g**inal | oran**g**e |

### <a name="english-approximants"></a>Angielskie przybliżenia

| `sapi` | `ipa` | Przykład 1          | Przykład 2  | Przykład 3 |
|--------|-------|--------------------|------------|-----------|
| l      | `l`   | **l**id, g**l**ad  | pa**l**ace | chi**ll** |
| r      | `ɹ`   | **r**ed, b**r**ing | bo**rr**ow | ta**r**   |

# <a name="fr-fr"></a>[fr-FR](#tab/fr-FR)

### <a name="french-suprasegmentals"></a>Francuskie suprasegmentals

Zestaw telefonów usługi mowy kładzie jednak nacisk na samogłoskę zestresowanej sylaby; Zestaw `fr-FR` telefonów usługi mowy nie obsługuje podszepu IPA "". Jeśli podtwartę IPA jest potrzebna, należy użyć IPA bezpośrednio.

### <a name="french-vowels"></a>Samogłoski francuskie

| `sapi` | `ipa` | Przykład 1     | Przykład 2       | Przykład 3 |
|--------|-------|---------------|-----------------|-----------|
| a      | `a`   | **rbre**     | p**a**tte       | ir**a**   |
| aa     | `ɑ`   |               | p**â**te        | p**a**s   |
| aa ~   | `ɑ̃`  | **pl**kłyk    | enf**en**t      | t**em**ps |
| Ax     | `ə`   |               | p**e**tite      | l**e**    |
| Eh     | `ɛ`   | **e**lle      | p**e**rdu       | ét**ai**t |
| Ue     | `ø`   | **œu**fs      | cr**eu**ser     | qu**eu**  |
| Ey     | `e`   | ému ( ému )           | crétin ( crétin )          | ôté (własówce)       |
| eh ~   | `ɛ̃`  | **im**portant | p**ein**ture    | mata**w** |
| Iy     | `i`   | **dée**      | zwierzę**i**te      | jestem**i**   |
| Oe     | `œ`   | **œu**f       | p**eu**r        |           |
| Oh     | `ɔ`   | **o**bstacle  | c**o**rps       |           |
| oh ~   | `ɔ̃`  | **na**ze      | r**na**deur     | b**na**   |
| Ow     | `o`   | **au**diteur  | b**eau**zamach stanu    | p**ô**    |
| oe ~   | `œ̃ ` | **Onz**        | l**un**di       | br**un**  |
| Uw     | `u`   | **ou**trage   | intr**ou**vable | **Ou**    |
| Uy     | `y`   | **u**ne       | p**u**nir       | él**u**   |

### <a name="french-consonants"></a>Spółgłoski francuskie

| `sapi` | `ipa` | Przykład 1   | Przykład 2     | Przykład 3                        |
|--------|-------|-------------|---------------|----------------------------------|
| b      | `b`   | **b**ête    | ha**b**ille   | ro**b**e                         |
| d      | `d`   | **d**ire    | ron**d**eur   | chau**d**e                       |
| k      | `f`   | **f**emme   | su**ff**ixe   | bo**f**                          |
| g      | `g`   | **g**auche  | é**g**ale     | ba**gu**e                        |
| Ng     | `ŋ`   |             |               | [<sup>1</sup>](#fr-1)**parkowanie** |
| Hy     | `ɥ`   | h**u**ile   | n**u**ire     |                                  |
| k      | `k`   | **c**arte   | é**c**aille   | być**c**                          |
| l      | `l`   | **l**ong    | é**l**ire     | ba**l**                          |
| m      | `m`   | **m**adame  | ai**m**er     | po**mm**e                        |
| n      | `n`   | **n**ous    | te**n**ir     | bo**nn**e                        |
| Nj     | `ɲ`   |             |               | pei**gn**e                       |
| p      | `p`   | **p**atte   | re**p**as     | ca**p**                          |
| r      | `ʁ`   | **r**w     | cha**r**iot   | senti**r**                       |
| s      | `s`   | **s**ourir  | **a ss**ez     | pa**ss**e                        |
| Sh     | `ʃ`   | **ch**anter | ma**ch**ine   | po**ch**e                        |
| t      | `t`   | **t**ête    | ô**t**er      | ne**t**                          |
| v      | `v`   | **v**ent    | w**v**enter  | rê**przeciwko**e                         |
| w      | `w`   | **ou**i     | f**ou**ine    |                                  |
| t      | `j`   | **y**od     | p**i**étiner  | Marsylia**ille**                    |
| z      | `z`   | **z **éro   | rai**s**onner | ro**s**e                         |
| Zh     | `ʒ`   | **j**ardin  | człowiek**g**er    | piè**g**e                        |
|        | `n‿`  |             |               | u**n** arbre                     |
|        | `t‿`  |             |               | kwas**d**                        |
|        | `z‿`  |             |               | di**x**                          |

<a id="fr-1"></a>
**1** *Tylko dla niektórych obcych słów.*

> [!TIP]
> Zestaw `fr-FR` telefonów usługi mowy nie obsługuje następujących francuskich `n‿` `t‿`liasions, , i `z‿`. Jeśli są one potrzebne, należy rozważyć użycie IPA bezpośrednio.

# <a name="de-de"></a>[de-DE](#tab/de-DE)

### <a name="german-suprasegmentals"></a>Niemieckie suprasegmentals

| Przykład 1 (Początek spółgłoski, początkowe słowo dla samogłoski) | Przykład 2 (Intervocalic dla spółgłoski, słowo jądro przyśrodkowe dla samogłoski) | Przykład 3 (Coda dla spółgłoski, słowo końcowe dla samogłoski) | Komentarze |
|--|--|--|--|
| anders /a **1** n - d ax r s/ | Multiplikationszeichen /m uh l - t iy - p l iy - k a - ts y ow **1** n s - ts ay - c n/ | Biologie /b iy - ow - l ow - g iy **1**/ | Zestaw telefonów do obsługi mowy położył nacisk po samogłosce zestresowanej sylaby |
| Allgemeinwissen /a **2** l - g ax - m ay 1 n - v ih - s n/ | Abfallentsorgungsfirma /a 1 p - f a l - ^ eh n t - z oh **2** ax r - g uh ng s - f ih ax r - m a/ | Computertomographie /k oh m - p y uw 1 - t ax r - t ow - m ow - g r a - f iy **2**/ | Zestaw telefonów do obsługi mowy położył nacisk po samogłosce sylaby podstresowej |

### <a name="german-vowels"></a>Samogłoski niemieckie

| `sapi` | `ipa`     | Przykład 1                             | Przykład 2     | Przykład 3                          |
|--------|-----------|---------------------------------------|---------------|------------------------------------|
| A:     | `aː`      | A ber **(ber)**                              | Maßst**a**b   | Schem**a**                         |
| a      | `a`       | A bfall **(A**bfall)                            | B**a**ch      | Agath**a**                         |
| Oh     | `ɔ`       | **O**sten                             | Pf**o**sten   |                                    |
| Eh:    | `ɛː`      | **Ä**hnlichkeit                       | B**ä**r       | [<sup>1.</sup>](#de-v-1) Fasci**ae** |
| Eh     | `ɛ`       | **ä**ndern                            | Proz**e**nt   | Ciało**migdałowate ae**                      |
| Ax     | `ə`       | [<sup>2</sup>](#de-v-2)'v**e**rstauen | Aach**e**n    | Frag**e (frag e)**                          |
| Iy     | `iː`      | **Pobiegłem**                              | abb**ie**gt   | Relativitätstheor**czyli**            |
| Ih     | `ɪ`       | **I**nnung                            | s**i**ngen    | Drewno**y**                          |
| Ue     | `øː`      | **Ö sen (ö**sen)                              | abl**ö**sten  | Malm**ö (Malm)**                          |
| Ow     | `o`, `oː` | **o**hne                              | Balk**o**n    | Trept**ow (trept ow)**                        |
| Oe     | `œ`       | **Ö**Ffnung ( ffnung )                           | bef**ö**rdern |                                    |
| Ey     | `e`, `eː` | E berhard **(E**berhard)                          | abf**e**gt    | b                                  |
| Uw     | `uː`      | **U**nie                               | H**u**t       | Akk**u**                           |
| Uh     | `ʊ`       | **U**nterschiedes                     | b**u**nt      |                                    |
| ue (ue)     | `yː`      | **Ü**bermut                           | pfl**ü**gt    | Mężczyźni**ü**                           |
| Uy     | `ʏ`       | **ü**ppig                             | S**y**łodyga    |                                    |

<a id="de-v-1"></a>
**1** *Tylko w słowach obcego pochodzenia, takich jak: Fasci**ae**.*<br>
<a id="de-v-2"></a>
**2** *Słowo intially tylko w słowach obcego pochodzenia, takich jak **ppointment.** Sylaba-początkowo w: 'v**e**rstauen.*

### <a name="german-diphthong"></a>Niemiecki diphthong

| `sapi` | `ipa`       | Przykład 1    | Przykład 2          | Przykład 3 |
|--------|-------------|--------------|--------------------|-----------|
| Ay     | `ai`        | **ei**nsam   | Unabhängigk**ei**t | Abt**ei (abt ei)** |
| Awh     | `au`        | **au**ßen    | abb**au**st        | Św**au**  |
| Oy     | `ɔy`, `ɔʏ̯` | **Eu**phorie | tr**äu**mt         | sch**eu** |

### <a name="german-semivowels"></a>Niemieckie semivowels

| `sapi` | `ipa` | Przykład 1 | Przykład 2    | Przykład 3  |
|--------|-------|-----------|--------------|------------|
| ax r   | `ɐ`   |           | abänd**er**n | **blokada** |

### <a name="german-consonants"></a>Spółgłoski niemieckie

| `sapi` | `ipa` | Przykład 1 | Przykład 2 | Przykład 3 |
|--|--|--|--|--|
| b | `b` | **B**ank |  | [<sup>1.</sup>](#de-c-1) Pu**b** |  |
| c | `ç` | Ch emie **(ch**emie) | mögli**ch**st | [<sup>2</sup>](#de-c-2)i**ch** |
| d | `d` | **d**anken | [<sup>3</sup>](#de-c-3) Len**d**l | [<sup>4</sup>](#de-c-4) Clau**d**e |  |
| Jh | `ʤ` | **J**eff | gemana**g**t | [<sup>5</sup>](#de-c-5) Chan**g**e |
| k | `f` | **F**ahrtdauer | angri**ff**slustig | abbruchrei**f** |  |
| g | `g` | **g**ut |  | [<sup>6</sup>](#de-c-6) Gre**g** |  |
| h | `h` | **H**ausanbau |  |  |  |
| t | `j` | **J**od | Reakt**i**na | hu**i** |  |
| k | `k` | **K**oma | Aspe**k**t | Flec**k** |  |
| l | `l` | **l**au | ähne**l**n | zuvie**l** |  |
| m | `m` | **M**ut | A**m**t | Leh**m** |  |
| n | `n` | **n**un | u**n**d | Huh**n** |  |
| Ng | `ŋ` | [<sup>7</sup>](#de-c-7)**Ng**uyen | Schwa**nk (Schwa nk)** | R**ing (właso)** |  |
| p | `p` | **P**artner | abru**p**t | Ti**p** |  |
| Pf | `pf` | **z**o.o. | tama**pf**t | Do**pf** |  |
| r | `ʀ`, `r`, `ʁ` | R eise **(r**eise) | knu**rr**t | Haa r ( Haa**r )** |  |
| s | `s` | [<sup>8</sup>](#de-c-8)**S**taccato | bi**s**t | mie**s** |  |
| Sh | `ʃ` | Sch ule **(Sch**ule) | mi**sch**t | lappi**sch** |  |
| t | `t` | T raum **(t**raum) | S**t**raße | Mu**t** |  |
| Ts | `ts` | **Z**ug | Ar**z**t | Dowcip**z** |  |
| ch | `tʃ` | Tsch echien **(Tsch**echien) | aufgepu**tsch**t | bundesdeu**tsch** |  |
| v | `v` | **w**inken | Q**u**alle | [<sup>9</sup>](#de-c-9) Gr**oo**ve |  |
| x | [<sup>10</sup>](#de-c-10)`x`,[<sup>11</sup>](#de-c-11)`ç` | [<sup>12</sup>](#de-c-12) Ba ch erach **(ba ch**erach) | Ma**ch**t mögli**ch**st | Schma**ch** 'i**ch** |
| z | `z` | **s**uper |  |  |  |
| Zh | `ʒ` | **G**enre | B**re**ezinski | Edvi**g**e |

<a id="de-c-1"></a>
**1** *Tylko w słowach obcego pochodzenia, takich jak: Pu**b**.*<br>
<a id="de-c-2"></a>
**2** *Miękkie "ch" po "e" i "i"*<br>
<a id="de-c-3"></a>
**3** *Tylko w słowach obcego pochodzenia, takich jak: Len**d**l.*<br>
<a id="de-c-4"></a>
**4** *Tylko w słowach obcego pochodzenia, takich jak: Clau**d**e.*<br>
<a id="de-c-5"></a>
**5** *Tylko w słowach obcego pochodzenia, takich jak: Chan**g**e.*<br>
<a id="de-c-6"></a>
**6** *Word-terminally tylko w słowach obcego pochodzenia, takich jak Gre**g**.*<br>
<a id="de-c-7"></a>
**7** *Tylko w słowach obcego pochodzenia, takich jak: **Ng**uyen.*<br>
<a id="de-c-8"></a>
**8** *Tylko w słowach obcego pochodzenia, takich jak: **S**taccato.*<br>
<a id="de-c-9"></a>
**9** *Tylko w słowach obcego pochodzenia, takich jak: Gr**oo**ve.*<br>
<a id="de-c-10"></a>
**10** *IPA `x` jest twardy "ch" po wszystkich samogłosek nie-front (a, aa, oh, ow, uh, uw i diphthong aw).*<br>
<a id="de-c-11"></a>
**11** *IPA `ç` jest miękkim "ch" po samogłosek przednich (ih, iy, eh, ae, uy, ue, oe, eu również w diphthongs ay, oy) i spółgłosek*<br>
<a id="de-c-12"></a>
**12** *Word-początkowo tylko w słowach obcego pochodzenia, takich jak: **J**uan. Sylaba-początkowo również w**słowach**takich jak: Ba ch erach.*<br>

### <a name="german-oral-consonants"></a>Niemieckie spółgłoski doustne

| `sapi` | `ipa` | Przykład 1                                  |
|--------|-------|--------------------------------------------|
| ^      | `ʔ`   | beachtlich /b ax - ^ a 1 x t - l ih c/ |

> [!NOTE]
> Musimy dodać [gs\] telefon między dwoma odrębnymi samogłoskami, z wyjątkiem dwóch samogłosek są prawdziwe diphthong. Ta spółgłoska doustna jest przystankiem glottal, aby uzyskać więcej informacji, patrz <a href="http://en.wikipedia.org/wiki/Glottal_stop" target="_blank">glottal stop <span class="docon docon-navigate-external x-hidden-focus"> </a> </a>.

# <a name="es-es"></a>[es-ES](#tab/es-ES)

### <a name="spanish-vowels"></a>Samogłoski hiszpańskie

| `sapi` | `ipa` | Przykład 1    | Przykład 2     | Przykład 3    |
|--------|-------|--------------|---------------|--------------|
| a      | `a`   | **a**lto     | c**a**ntar    | cas**a**     |
| mogę      | `i`   | **i**bérica  | av**i**spa    | podatek**i**     |
| e      | `e`   | **e**lefante | w:**e**nto    | elefant**e** |
| o      | `o`   | **o**caso    | enc**o**ntrar | ocasenc**o** |
| u      | `u`   | **u**sted    | p**u**nta     | Juanl**u**   |

### <a name="spanish-consonants"></a>Spółgłoski hiszpańskie

| `sapi` | `ipa`      | Przykład 1  | Przykład 2      | Przykład 3      |
|--------|------------|------------|----------------|----------------|
| b      | `b`        | **b**aobab |                | jestem**b**        |
|        | `β`        |            | bao**b**ab     | baoba**b**     |
| ch     | `tʃ`       | **ch**eque | co**ch**e      | Marraque**ch (marraque ch)** |
| d      | `d`        | **d**edo   |                | portlan**d**   |
|        | `ð`        |            | de**d**o       | verda**d**     |
| k      | `f`        | **f**ácil  | ele**f**ante   | pu**f**        |
| g      | `g`        | **g**anga  |                | dópin**g**     |
|        | `ɣ`        |            | a**g**ua       | tuare**g**     |
| J      | `j`        | **i**odo   | cal**i**ente   | re**y**        |
| Jj     | `j.j` `jj` |            | vi**ll**a      |                |
| k      | `k`        | **c**oche  | bo**c**a       | titáni**c**    |
| l      | `l`        | **l**ápiz  | a**l**a        | corde**l**     |
| Ll     | `ʎ`        | **ll**ave  | desarro**ll**o |                |
| m      | `m`        | **m**zamówienie | a**m**ar       | álbu**m**      |
| n      | `n`        | **n**ada   | ce**n**a       | rató**n**      |
| Nj     | `ɲ`        | **ñ**aña   | ara**ñ**azo    |                |
| p      | `p`        | **p**oca   | do**p**o       | sto**p**       |
| r      | `ɾ`        |            | ca**r**a       | abri**r**      |
| Rr     | `r`        | **r**adio  | co**rr**e      | pu**rr**       |
| s      | `s`        | **s**aco   | va**s**o       | pelo**s**      |
| t      | `t`        | **t**oldo  | a**t**ar       | disque**t**    |
| Th     | `θ`        | **z**ebra  | a**z**ul.       | lápi**z**      |
| w      | `w`        | h**u**eso  | ag**u**a       | gua**u**       |
| x      | `x`        | **j**ota   | a**j**o        | relo**j**      |

> [!TIP]
> Zestaw `es-ES` telefonów usługi mowy nie obsługuje następujących `β`hiszpańskich IPA, i `ð` `ɣ`. Jeśli są one potrzebne, należy rozważyć użycie IPA bezpośrednio.

# <a name="zh-cn"></a>[zh-CN](#tab/zh-CN)

Zestaw telefonów usługi `zh-CN` mowy jest oparty na natywnym zestawie <a href="https://en.wikipedia.org/wiki/Pinyin" target="_blank">pinyin <span class="docon docon-navigate-external x-hidden-focus"></span> </a> telefonu.

### <a name="tone"></a>Ton komunikatu

| Ton Pinyin | `sapi` | Przykład postaci |
|-------------|--------|-------------------|
| mā (mā)          | ma 1  | z o.o.                 |
| má (má)          | ma 2  | z o.o.                 |
| m          | ma 3  | z o.o.                 |
| mà (mà)          | ma 4  | z o.o.                 |
| ma          | ma 5  | z o.o.                 |

#### <a name="example"></a>Przykład

| Znak | Usługa rozpoznawania mowy                |
|-----------|-------------------------------|
| 中田田田      | zu 3 - zhi 1 - guan 1 - xi 5 |
| 中田田        | lei 3 -jin 4                 |
| 中田田田       | xi 1 - zhai 2 - xiang 4      |

# <a name="zh-tw"></a>[zh-TW](#tab/zh-TW)

Zestaw telefonów usługi `zh-TW` mowy jest oparty na natywnym zestawie <a href="https://en.wikipedia.org/wiki/Bopomofo" target="_blank">bopomofo <span class="docon docon-navigate-external x-hidden-focus"></span> </a> telefonu.

### <a name="tone"></a>Ton komunikatu

| Ton usługi mowy | Ton Bopomofo | Przykład (słowo) | Telefony z usługami mowy | Bopomofo | Pinyin (jap.) |
|---------------------|---------------|----------------|-----------------------|----------|-------------|
| z o.o.                   | empty         | z o.o.              | z o.o.                   | z o.o.       | zhēn ( zhēn )        |
| z o.o.                   | z o.o.             | z o.o.              | z o.o.                   | z o.o.      | chá (chá)         |
| z o.o.                   | z o.o.             | z o.o.              | z o.o.                   | z o.o.      | d          |
| z o.o.                   | z o.o.             | z o.o.              | z o.o.                   | z o.o.      | wàng        |
| ˙                   | ˙             | 19.00.             | z o.o.               | z o.o.  | yng zi    |

#### <a name="example"></a>Przykład

| Znak | `sapi`   |
|-----------|----------|
| z o.o.         | z o.o.      |
| z o.o.        | z o.o.   |
| 中田田        | z o.o. |

# <a name="ja-jp"></a>[ja-JP](#tab/ja-JP)

Zestaw telefonów usługi `ja-JP` mowy jest oparty na natywnym zestawie <a href="https://en.wikipedia.org/wiki/Kana" target="_blank">telefonu Kana. <span class="docon docon-navigate-external x-hidden-focus"></span> </a>

### <a name="stress"></a>Stres

| `sapi` | `ipa`          |
|--------|----------------|
| `ˈ`    | `ˈ`podporowa |
| `+`    | `ˌ`podksyt  |

#### <a name="example"></a>Przykład

| Znak | `sapi`  | `ipa`       |
|-----------|---------|-------------|
| 19.00.        | 中田田田    | go(-ychi)   |
| 中田田田       | 中田田??? | w 1998 roku, w 1999 roku, w 1999 roku, w 199 |
| 中田田       | 中田田田田+  | sajitecika |

***