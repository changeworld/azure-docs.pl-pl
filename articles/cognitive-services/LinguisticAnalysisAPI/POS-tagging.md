---
title: Część z mowy znakowanie językową analiza interfejsu API | Dokumentacja firmy Microsoft
description: Dowiedz się, jak znakowanie części z mowy w usługach kognitywnych Microsoft identyfikuje kategorii lub część mowy każdego wyrazu tekstu.
services: cognitive-services
author: RichardSunMS
manager: wkwok
ms.service: cognitive-services
ms.component: linguistic-analysis
ms.topic: article
ms.date: 09/27/2016
ms.author: lesun
ms.openlocfilehash: 90fd5b05c2dabdac88c6c8da288ab629177be38d
ms.sourcegitcommit: d1eefa436e434a541e02d938d9cb9fcef4e62604
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/28/2018
ms.locfileid: "37082642"
---
# <a name="part-of-speech-tagging"></a>Znakowanie części z mowy

## <a name="background-and-motivation"></a>Tło i Motywacją

Tekst zostało rozdzielone zdań i tokeny, następnym krokiem analizy po do identyfikowania kategorii lub części z mowy każdego wyrazu.
Obejmują one kategorie, takie jak *rzeczownik* (zazwyczaj reprezentujący osoby, miejsca, rzeczy, pomysły, itp.) i *zlecenie* (zazwyczaj reprezentujące akcje, zmiany stanu itd. Niektóre wyrazów część mowy jest jednoznaczne (dla wystąpienia *quagmire* to naprawdę tylko rzeczownikiem), ale dla wielu innych jest stwierdzić.
*Tabela* można to miejsce, w którym sit (lub układ 2-D cyfry), ale można również "Tabela dyskusji".

## <a name="list-of-part-of-speech-tags"></a>Lista tagów części z mowy

| Tag | Opis | Przykład słowa |
|-----|-------------|---------------|
| $ | dolara ($) | $ |
| \`\` | cudzysłów otwierający | \` \`\` |
| '' | zamykającego znaku cudzysłowu | ' '' |
| ( | nawias otwierający | ( [ { |
| ) | nawias zamykający | ) ] } |
| , | przecinkami | , |
| -- | kreska | -- |
| . | terminator zdanie | . ! ? |
| : | dwukropek lub wielokropka | : ; ... |
| DW | połączeniu koordynowanie | i, ale lub jeszcze|
| DYSKU CD | cyfry kardynalnej | dziewięć 1980 20 "96 |
| DT | determiner |wszystkie zarówno ani|
| EX | egzystencjalna miejsca | Brak |
| ŚRODOWISKA .NET FRAMEWORK | obcy programu word | enfant terrible hoi polloi je ne sais quoi |
| W | preposition lub włączających połączeniu| w elemencie Jeśli po czy |
| JJ | przymiotnik lub liczby porządkowej | dziewiąty pretty execrable wielomodalnymi |
| JJR | przymiotnik porównawczych | lepiej szybciej tańsze |
| JJS | przymiotnik superlative | najlepsze najszybciej najtańszej | 
| LS | Znacznik elementów listy | (b) 1 2 A B A. B. |
| MD | modalne pomocniczych | można mogą są będzie można może powinien powinna |
| NN | rzeczownik, typowe, liczbie pojedynczej lub masowej | buta pieniędzy ziemniaka |
| NNP | rzeczownik, pojedynczego właściwe, | Kennedy Roosevelt Chicago Weehauken |
| NNPS | rzeczownik, właściwe, w liczbie mnogiej | Krzewy Springfields |
| NNS | rzeczownik, typowych, w liczbie mnogiej | elementy pola myszy |
| (czas pacyficzny) | wstępne determiner | wszystkie zarówno połowie wiele takich dość się, że to |
| POS | genitive znacznika | "w |
| ZASADY REPLIKACJI HASEŁ | zrozumieć osobiste | Użytkownik on go I możemy ich możesz |
| ZASADY REPLIKACJI HASEŁ$ | zrozumieć dzierżawczego | hers jego jego Moje naszych ich sieci |
| RB | Parametr | klinicznie tylko |
| RBR | Parametr porównawczych | dalsze gloomier grander graver większa grimmer przeszkodę poważniejszych zdrowsze większych wyższej jednak większe nowsze leaner dłuższy mniej doskonale mniejszym lonelier dłużej głośność dolna więcej... |
| SPZ | Parametr superlative | najlepsze największych bluntest najwcześniejszą najdalszych pierwszy najdalej najtrudniejsze heartiest najwyższy największy najmniej mniej większość najbliższej najgorszy drugi najbardziej restrykcyjne metody |
| PLANU ODZYSKIWANIA | Cząstka | wyłączone na górę limit o |
| SYM | Symbol | % & |
| DO | "do" jako znacznik preposition lub infinitive | na |
| NIESTETY | interjection | Niestety doskonale howdy hello |
| VB | zlecenie, formularza podstawowego | Nadaj bieżąco przypisania |
| VBD | zlecenie, przeszłego | nadać przypisanej flew |
| VBG | zlecenie, obecnie participle lub gerund | nadanie upływa przypisywanie |
| VBN | zlecenie, participle w przeszłości | podane przypisanej znajdowały się powietrzu |
| VBP | zlecenie, teraźniejszym, osoby nie 3 pojedynczej | Nadaj bieżąco przypisania |
| VBZ | zlecenie, teraźniejszym, 3 pojedynczej osoby | zapewnia przypisuje prostej |
| WDT | KI determiner | który jakie którego |
| WP | KI zrozumieć | kto którego |
| WP$ | KI zrozumieć dzierżawczego | którego |
| WRB | Ki zlecenia | jak jednak zawsze, gdy gdzie |

## <a name="specification"></a>Specyfikacja

Podobnie jak w przypadku tokenizacji, możemy zależą od specyfikacji z [Penn Treebank](https://catalog.ldc.upenn.edu/ldc99t42).
