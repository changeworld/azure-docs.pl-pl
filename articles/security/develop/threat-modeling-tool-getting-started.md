---
title: Wprowadzenie — narzędzie Microsoft Threat Modeling Tool — Azure | Dokumenty firmy Microsoft
description: Jest to głębszy przegląd podkreślając narzędzie do modelowania zagrożeń w akcji.
services: security
documentationcenter: na
author: jegeib
manager: jegeib
editor: jegeib
ms.assetid: na
ms.service: security
ms.subservice: security-develop
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/17/2017
ms.author: jegeib
ms.openlocfilehash: 1454826095bcced9b20935405c0befd5a1ed1ddd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "68728324"
---
# <a name="getting-started-with-the-threat-modeling-tool"></a>Wprowadzenie do narzędzia do modelowania zagrożeń

Narzędzie Microsoft Threat Modeling Tool 2018 zostało wydane jako GA we wrześniu 2018 r. jako bezpłatne **[narzędzie click-to-download](https://aka.ms/threatmodelingtool)**. Zmiana mechanizmu dostawy pozwala nam wypychać najnowsze ulepszenia i poprawki błędów do klientów za każdym razem, gdy otwierają narzędzie, ułatwiając konserwację i korzystanie.
W tym artykule przedstawiono proces rozpoczynania pracy z podejściem do modelowania zagrożeń SDL firmy Microsoft i pokazano, jak używać tego narzędzia do opracowywania modeli wielkich zagrożeń jako szkieletu procesu zabezpieczeń.

Ten artykuł opiera się na istniejącej wiedzy na temat metody modelowania zagrożeń SDL. Aby uzyskać szybki przegląd, zobacz **[Aplikacje sieci Web modelowania zagrożeń](https://msdn.microsoft.com/library/ms978516.aspx)** i zarchiwizowana wersja **[funkcji Odkrywanie wad zabezpieczeń przy użyciu](https://docs.google.com/viewer?a=v&pid=sites&srcid=ZGVmYXVsdGRvbWFpbnxzZWN1cmVwcm9ncmFtbWluZ3xneDo0MTY1MmM0ZDI0ZjQ4ZDMy)** artykułu MSDN podejścia STRIDE opublikowanego w 2006 r.

Aby szybko podsumować, podejście obejmuje tworzenie diagramu, identyfikowanie zagrożeń, łagodzenie ich i sprawdzanie poprawności każdego łagodzenia. Oto diagram, który podkreśla ten proces:

![Proces SDL](./media/threat-modeling-tool-getting-started/sdlapproach.png)

## <a name="starting-the-threat-modeling-process"></a>Uruchamianie procesu modelowania zagrożeń

Po uruchomieniu narzędzia do modelowania zagrożeń zauważysz kilka rzeczy, co widać na rysunku:

![Pusta strona początkowa](./media/threat-modeling-tool-feature-overview/tmtstart.png)

### <a name="threat-model-section"></a>Sekcja Modelu zagrożenia

| Składnik                                   | Szczegóły                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       |
| ------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Przycisk Opinie, Sugestie i Problemy** | Zabierze Cię na **[forum MSDN](https://social.msdn.microsoft.com/Forums/en-US/home?forum=sdlprocess)** dla wszystkich rzeczy SDL. Daje możliwość zapoznania się z tym, co robią inni użytkownicy, wraz z obejściem i zaleceniami. Jeśli nadal nie możesz znaleźć tego, czego tmtextsupport@microsoft.com szukasz, wyślij wiadomość e-mail do naszego zespołu pomocy technicznej, aby Ci pomóc                                                                                                                            |
| **Tworzenie modelu**                          | Otwiera pustą kanwę, aby narysować diagram. Upewnij się, że wybierzesz szablon, którego chcesz użyć dla swojego modelu                                                                                                                                                                                                                                                                                                                                                                       |
| **Szablon dla nowych modeli**                 | Przed utworzeniem modelu należy wybrać szablon do użycia. Nasz główny szablon to szablon modelu zagrożeń platformy Azure, który zawiera wzorniki, zagrożenia i środki zaradcze specyficzne dla platformy Azure. W przypadku modeli ogólnych wybierz bazę wiedzy TM SDL z menu rozwijanego. Chcesz utworzyć własny szablon lub przesłać nowy szablon dla wszystkich użytkowników? Aby dowiedzieć się więcej, zapoznaj się ze stroną GitHub **[repozytorium szablonów](https://github.com/Microsoft/threat-modeling-templates)**                              |
| **Otwieranie modelu**                            | <p>Otwiera wcześniej zapisane modele zagrożeń. Funkcja Ostatnio otwarte modele jest świetna, jeśli chcesz otworzyć najnowsze pliki. Po najechaniu kursorem na zaznaczenie znajdziesz 2 sposoby otwierania modeli:</p><p><ul><li>Otwórz z tego komputera – klasyczny sposób otwierania pliku przy użyciu pamięci lokalnej</li><li>Otwieranie z usługi OneDrive — zespoły mogą używać folderów w usłudze OneDrive do zapisywania i udostępniania wszystkich modeli zagrożeń w jednej lokalizacji, aby zwiększyć produktywność i współpracę</li></ul></p> |
| **Wprowadzenie — przewodnik**                   | Otwiera stronę główną **[narzędzia Microsoft Threat Modeling Tool](threat-modeling-tool.md)**                                                                                                                                                                                                                                                                                                                                                                                            |

### <a name="template-section"></a>Sekcja Szablon

| Składnik               | Szczegóły                                                                                                                                                          |
| ----------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Utwórz nowy szablon** | Otwiera pusty szablon do wykorzystania. Jeśli nie masz rozległej wiedzy w zakresie tworzenia szablonów od podstaw, zalecamy tworzenie na podstawie istniejących |
| **Otwórz szablon**       | Otwiera istniejące szablony, aby wprowadzić zmiany                                                                                                              |

Zespół narzędzia do modelowania zagrożeń stale pracuje nad poprawą funkcjonalności i doświadczenia narzędzia. Kilka drobnych zmian może mieć miejsce w ciągu roku, ale wszystkie główne zmiany wymagają przepisać w przewodniku. Często się z nim odwołuje, aby upewnić się, że otrzymujesz najnowsze ogłoszenia.

## <a name="building-a-model"></a>Tworzenie modelu

W tej sekcji obserwujemy:

- Cristina (programista)
- Ricardo (kierownik programu) i
- Ashish (tester)

Przechodzą przez proces opracowywania swojego pierwszego modelu zagrożenia.

> Ricardo: Cześć Cristina, pracowałem nad schematem modelu zagrożenia i chciałem się upewnić, że mamy szczegóły w prawo. Czy możesz mi pomóc spojrzeć na to?
> Cristina: Absolutnie. Spójrzmy na przykład.
> Ricardo otwiera narzędzie i dzieli się swoim ekranem z Cristiną.

![Podstawowy model zagrożenia](./media/threat-modeling-tool-feature-overview/basictmt.png)

> Cristina: Ok, wygląda prosto, ale można mnie przez to?
> Ricardo: Jasne! Oto podział:
> - Nasz ludzki użytkownik jest rysowany jako jednostka zewnętrzna — kwadrat
> - Wysyłają polecenia na nasz serwer www — krąg
> - Serwer sieci Web przegląda bazę danych (dwa równoległe linie)

Co Ricardo właśnie pokazał Cristina jest DFD, skrót od **[Data Flow Diagram](https://en.wikipedia.org/wiki/Data_flow_diagram)**. Narzędzie do modelowania zagrożeń umożliwia użytkownikom określenie granic zaufania, wskazywanych czerwonymi kropkowanymi liniami, aby pokazać, gdzie różne jednostki mają kontrolę. Na przykład administratorzy IT wymagają systemu usługi Active Directory do celów uwierzytelniania, więc usługa Active Directory znajduje się poza ich kontrolą.

> Cristina: Wygląda na to, że jest to dla mnie słuszne. A co z zagrożeniami?
> Ricardo: Pozwólcie, że wam pokażę.

## <a name="analyzing-threats"></a>Analizowanie zagrożeń

Po kliknięciu na widok analizy z wyboru menu ikony (plik z lupą), jest on przejmujący do listy wygenerowanych zagrożeń Narzędzie do modelowania zagrożeń znalezione na podstawie domyślnego szablonu, który używa podejścia SDL o nazwie **[STRIDE (Spoofing, Manipulacji, Ujawnienie informacji, Odrzucenie, Odmowa usługi i podniesienie uprawnień)](https://en.wikipedia.org/wiki/STRIDE_(security))**. Chodzi o to, że oprogramowanie jest pod przewidywalnym zestawem zagrożeń, które można znaleźć za pomocą tych 6 kategorii.

Takie podejście jest jak zabezpieczenie domu poprzez zapewnienie, że każde drzwi i okno mają mechanizm blokujący przed dodaniem systemu alarmowego lub pościgiem za złodziejem.

![Podstawowe zagrożenia](./media/threat-modeling-tool-getting-started/basicthreats.png)

Ricardo rozpoczyna się od wybrania pierwszego elementu na liście. Oto, co się dzieje:

Po pierwsze, interakcja między tymi dwoma wzornikami jest wzmocniona

![Interakcja](./media/threat-modeling-tool-getting-started/interaction.png)

Po drugie, dodatkowe informacje o zagrożeniu pojawiają się w oknie Właściwości zagrożenia

![Informacje o interakcji](./media/threat-modeling-tool-getting-started/interactioninfo.png)

Wygenerowane zagrożenie pomaga mu zrozumieć potencjalne wady konstrukcyjne. Kategoryzacja STRIDE daje mu wyobrażenie o potencjalnych wektorach ataku, podczas gdy dodatkowy opis mówi mu dokładnie, co jest nie tak, wraz z potencjalnymi sposobami złagodzenia go. Może używać edytowalnych pól do pisania notatek w szczegółach uzasadnienia lub zmieniania ocen priorytetów w zależności od paska błędów w organizacji.

Szablony platformy Azure mają dodatkowe szczegóły, aby pomóc użytkownikom zrozumieć nie tylko, co jest nie tak, ale także jak to naprawić, dodając opisy, przykłady i hiperłącza do dokumentacji specyficznej dla platformy Azure.

Opis uświadomił mu znaczenie dodania mechanizmu uwierzytelniania, aby zapobiec fałszowaniu użytkowników, ujawniając pierwsze zagrożenie, nad którym należy pracować. Kilka minut do dyskusji z Cristina, zrozumieli znaczenie wdrażania kontroli dostępu i ról. Ricardo wypełnił kilka szybkich notatek, aby upewnić się, że zostały one wdrożone.

Gdy Ricardo zajął się zagrożeniami w ramach ujawniania informacji, zdał sobie sprawę, że plan kontroli dostępu wymaga pewnych kont tylko do odczytu do generowania audytu i raportu. Zastanawiał się, czy to powinno być nowe zagrożenie, ale środki zaradcze były takie same, więc zauważył zagrożenie odpowiednio.
Myślał również o ujawnieniu informacji nieco bardziej i zdał sobie sprawę, że taśmy kopii zapasowych będą wymagały szyfrowania, zadania dla zespołu operacyjnego.

Zagrożenia nie dotyczy projektu ze względu na istniejące środki zaradcze lub gwarancje zabezpieczeń można zmienić na "Nie dotyczy" z listy rozwijanej Stan. Istnieją trzy inne opcje: Nie rozpoczęty - domyślny wybór, Badanie potrzeb - używany do śledzenia elementów i Złagodzony - gdy jest w pełni pracował.

## <a name="reports--sharing"></a>Udostępnianie raportów &

Gdy Ricardo przechodzi przez listę z Cristina i dodaje ważne notatki, łagodzenia / uzasadnienia, priorytet i zmiany stanu, wybiera Raporty -> Tworzenie pełnego raportu -> Zapisz raport, który drukuje miły raport dla niego przejść z kolegami w celu zapewnienia odpowiedniej pracy bezpieczeństwa jest realizowany.

![Informacje o interakcji](./media/threat-modeling-tool-feature-overview/report.png)

Jeśli Ricardo chce udostępnić plik, może to łatwo zrobić, zapisując je na koncie usługi OneDrive w swojej organizacji. Gdy to zrobi, może skopiować link do dokumentu i udostępnić go swoim kolegom. 

## <a name="threat-modeling-meetings"></a>Spotkania modelowania zagrożeń

Kiedy Ricardo wysłał swój model zagrożenia do swojego kolegi za pomocą OneDrive, Ashish, tester, był underwhelmed. Wydawało się, że Ricardo i Cristina brakowało sporo ważnych przypadkach rogu, które mogą być łatwo zagrożone. Jego sceptycyzm jest uzupełnieniem modeli zagrożeń.

W tym scenariuszu, po Ashish przejął model zagrożeń, wezwał do dwóch spotkań modelowania zagrożeń: jedno spotkanie do synchronizacji procesu i przejść przez diagramy, a następnie drugie spotkanie do przeglądu zagrożeń i sign-off.

W pierwszym spotkaniu Ashish spędził 10 minut na przejściu wszystkich przez proces modelowania zagrożeń SDL. Następnie podciągnął schemat modelu zagrożeń i zaczął szczegółowo go wyjaśniać. W ciągu pięciu minut zidentyfikowano ważny brakujący składnik.

Kilka minut później Ashish i Ricardo wdali się w rozszerzoną dyskusję na temat tego, jak zbudowano serwer www. Nie był to idealny sposób na przebieg spotkania, ale wszyscy w końcu zgodzili się, że wczesne odkrycie rozbieżności pozwoli im zaoszczędzić czas w przyszłości.

Podczas drugiego spotkania zespół przeszedł przez zagrożenia, omówił kilka sposobów rozwiązania ich i podpisał się na modelu zagrożenia. Sprawdzili dokument w kontroli źródła i kontynuowali rozwój.

## <a name="thinking-about-assets"></a>Myślenie o aktywach

Niektórzy czytelnicy, którzy mają zagrożenie modelowane może zauważyć, że nie rozmawialiśmy o aktywach w ogóle. Odkryliśmy, że wielu inżynierów oprogramowania rozumie swoje oprogramowanie lepiej niż rozumieją pojęcie zasobów i jakie zasoby mogą być zainteresowane przez osobę atakującą.

Jeśli zamierzasz grozić modelem domu, możesz zacząć od myślenia o swojej rodzinie, niezastąpionych zdjęciach lub cennych dziełach sztuki. Być może zaczniesz od zastanowienia się nad tym, kto może się włamać i obecny system zabezpieczeń. Możesz też zacząć od uwzględnienia fizycznych cech, takich jak basen lub ganek. Są one analogiczne do myślenia o zasobach, atakujących lub projektowaniu oprogramowania. Każde z tych trzech podejść działa.

Podejście do modelowania zagrożeń, które tutaj przedstawiliśmy, jest znacznie prostsze niż to, co firma Microsoft robiła w przeszłości. Odkryliśmy, że podejście do projektowania oprogramowania działa dobrze dla wielu zespołów. Mamy nadzieję, że to twoje.

## <a name="next-steps"></a>Następne kroki

Wyślij swoje pytania, uwagi tmtextsupport@microsoft.comi wątpliwości do . **[Pobierz](https://aka.ms/threatmodelingtool)** narzędzie do modelowania zagrożeń, aby rozpocząć.
