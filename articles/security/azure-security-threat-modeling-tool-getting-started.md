---
title: Wprowadzenie do platformy Azure — narzędzia do modelowania zagrożeń firmy Microsoft — | Dokumentacja firmy Microsoft
description: Jest to bardziej Przegląd, wyróżnianie narzędzie do modelowania zagrożeń w działaniu.
services: security
documentationcenter: na
author: jegeib
manager: jegeib
editor: jegeib
ms.assetid: na
ms.service: security
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/17/2017
ms.author: jegeib
ms.openlocfilehash: 6315e6d39a3b68854beb6563d075e3c79ca93a69
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60610756"
---
# <a name="getting-started-with-the-threat-modeling-tool"></a>Wprowadzenie do narzędzia do modelowania zagrożeń

2018 modelowania narzędzia w programie Microsoft Threat został wydany jako wersji Ogólnodostępnej w września 2018 jako wolną  **[kliknij do pobierania](https://aka.ms/threatmodelingtool)** . Zmiana mechanizm dostarczania pozwala nam wypychania najnowsze ulepszenia i poprawki dla klientów każdym otwarciu narzędzia, dzięki czemu łatwiejsze do utrzymania i użycia.
Ten artykuł przeprowadzi Cię przez proces wprowadzenie zagrożeń firmy Microsoft SDL podejście do modelowania i pokazano, jak użyć narzędzia do tworzenia modeli doskonałe zagrożeń szkieletu do procesu z zabezpieczeniami.

W tym artykule jest oparta na wiedzy na temat zagrożenia SDL modelowania podejście. Szybki przegląd można znaleźć **[aplikacji sieci Web do modelowania zagrożeń](https://msdn.microsoft.com/library/ms978516.aspx)** zarchiwizowane wersję **[odkryć zabezpieczeń wady przy użyciu podejścia STRIDE](https://docs.google.com/viewer?a=v&pid=sites&srcid=ZGVmYXVsdGRvbWFpbnxzZWN1cmVwcm9ncmFtbWluZ3xneDo0MTY1MmM0ZDI0ZjQ4ZDMy)** Opublikowany artykuł w witrynie MSDN w 2006 r.

Aby podsumować zapotrzebowanie na szybkie, podejścia polega na tworzenia diagramu, identyfikowania zagrożeń, łagodzenia je i sprawdzanie poprawności każdy środek ograniczający ryzyko. Oto diagram, który wyróżnia tego procesu:

![Proces SDL](./media/azure-security-threat-modeling-tool-feature-overview/sdlapproach.png)

## <a name="starting-the-threat-modeling-process"></a>Uruchamianie zagrożeń procesu modelowania

Gdy możesz uruchomić narzędzie do modelowania zagrożeń, zauważysz kilka rzeczy, jak pokazano na ilustracji:

![Strona początkowa pusta](./media/azure-security-threat-modeling-tool-feature-overview/tmtstart.png)

### <a name="threat-model-section"></a>Sekcja model zagrożeń

| Składnik                                   | Szczegóły                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       |
| ------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Opinię, sugestie i przycisk problemów** | Przejście **[forum MSDN dotyczącym](https://social.msdn.microsoft.com/Forums/en-US/home?forum=sdlprocess)** kompleksowymi SDL. Daje to możliwość zapoznaj się z artykułem co robią inni użytkownicy, oraz rozwiązania i zalecenia. Jeśli nadal nie możesz znaleźć czego szukasz, Wyślij wiadomość e-mail tmtextsupport@microsoft.com dla naszego zespołu pomocy technicznej ułatwić                                                                                                                            |
| **Tworzenie modelu**                          | Zostanie otwarty pustej kanwy dla Ciebie narysować diagram. Pamiętaj wybrać szablon, którego chcesz użyć dla modelu                                                                                                                                                                                                                                                                                                                                                                       |
| **Szablon dla nowych modeli**                 | Musisz wybrać szablon przed utworzeniem modelu. Nasze główne to szablon modelu zagrożeń Azure, zawierającą wzorników specyficzne dla platformy Azure, zagrożenia i środki zaradcze. W przypadku ogólnych modeli wybierz TM SDL wiedzy z menu rozwijanego. Czy chcesz utworzyć własny szablon lub Prześlij nową dla wszystkich użytkowników? Zapoznaj się z naszym **[repozytorium szablonów](https://github.com/Microsoft/threat-modeling-templates)** strony GitHub, aby dowiedzieć się więcej                              |
| **Otwieranie modelu**                            | <p>Zostanie otwarta wcześniej zapisać modele zagrożeń. Funkcja ostatnio otwarte modeli jest świetny, jeśli potrzebujesz otwarcie najnowszych plików. Po umieszczeniu wskaźnika myszy zaznaczenie, znajdziesz 2 sposoby otwierania modele:</p><p><ul><li>Otwórz z tego komputera — klasyczny sposób otwierania pliku przy użyciu magazynu lokalnego</li><li>Otwórz w usłudze OneDrive — zespoły mogą korzystać folderów w usłudze OneDrive do zapisywania i udostępniania ich modele zagrożeń w obrębie jednej lokalizacji, aby pomóc w zwiększeniu produktywności i współpracy</li></ul></p> |
| **Wprowadzenie — przewodnik**                   | Otwiera **[narzędzie do modelowania zagrożeń firmy Microsoft](./azure-security-threat-modeling-tool.md)** strony głównej                                                                                                                                                                                                                                                                                                                                                                                            |

### <a name="template-section"></a>Sekcja szablonu

| Składnik               | Szczegóły                                                                                                                                                          |
| ----------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Utwórz nowy szablon** | Otwiera pusty szablon do tworzenia. Jeśli nie masz obszerną wiedzę na temat tworzenia szablonów od podstaw, zalecamy opracowano na podstawie istniejących |
| **Otwórz szablon**       | Zostanie otwarty istniejących szablonów służących do wprowadzania zmian w                                                                                                              |

Zespół narzędzie do modelowania zagrożeń stale pracuje się w odniesieniu do poprawy funkcjonalności narzędzia i środowisko. Kilka niewielkie zmiany może mieć miejsce w ciągu roku, ale wszystkie istotne zmiany wymagają modyfikacji oprogramowania w przewodniku. Zapoznaj się często, aby upewnić się, że możesz uzyskać najnowsze informacje.

## <a name="building-a-model"></a>Budowanie modelu

W tej sekcji firma Microsoft wykonaj następujące czynności:

- Cristina (dla deweloperów)
- Ricardo (Menedżer programu) i
- Ashish (tester)

Ich przechodzenia przez proces tworzenia ich pierwszy model zagrożeń.

> Ricardo: Cristina cześć I pracuje diagram modelu zagrożeń i chciała upewnij się, że mamy szczegóły prawo. Proszę o pomoc go przejrzeć?
> Cristina: Naturalnie. Przyjrzyjmy się.
> Ricardo zostanie otwarte narzędzie i udostępnia Cristina swojego ekranu.

![Model zagrożeń podstawowe](./media/azure-security-threat-modeling-tool-feature-overview/basictmt.png)

> Cristina: Dobrze, wygląda prosty, ale możesz zapoznać się ze mną go?
> Ricardo: Się! Poniżej przedstawiono podział:
> - Nasze ludzi użytkownika jest rysowana w formie poza jednostki — kwadrat
> - Są one wysyłania poleceń do serwera sieci Web — okręgu
> - Serwer sieci Web jest konsultacji bazy danych (dwie linie)

Co Ricardo właśnie pokazałeś Cristina jest DPD, mała w przypadku  **[Diagram przepływu danych](https://en.wikipedia.org/wiki/Data_flow_diagram)** . Narzędzie do modelowania zagrożeń umożliwia użytkownikom podanie granice zaufania, wskazywanym przez czerwone linie kropkowana, aby pokazać, gdzie są różnymi jednostkami w formancie. Na przykład Administratorzy IT wymagają systemu usługi Active Directory na potrzeby uwierzytelniania, dzięki usłudze Active Directory znajduje się poza jej kontrolą.

> Cristina: Wygląda prawo do mnie. Jak wygląda zagrożeń?
> Ricardo: Pozwól mi pokazano.

## <a name="analyzing-threats"></a>Analiza zagrożeń

Po kliknięciu przycisku w widoku analizy z zaznaczenia menu ikonę (plik z lupę), jest on przekierowanie do listy wygenerowanych zagrożeń narzędzie do modelowania zagrożeń znaleziono oparty na szablonie domyślnym, korzystającą z podejściem SDL o nazwie  **[ STRIDE (fałszowaniem/przechwyceniem, Zmanipulowaniem, ujawnienia informacji, możliwość wyparcia, odmowę usługi i podniesienie uprawnień)](https://en.wikipedia.org/wiki/STRIDE_(security))** . Chodzi o to, że oprogramowanie jest dostępna w ramach przewidywalnego zbiór zagrożenia, które można znaleźć, używając tych kategorii 6.

To podejście jest takie, jak zabezpieczanie Twoim domu, zapewniając każdego drzwi i okno ma mechanizm blokowania przed dodaniem system alarmowy lub wyszukiwać te elementy po złodziej będzie.

![Podstawowe zagrożenia](./media/azure-security-threat-modeling-tool-feature-overview/basicthreats.png)

Ricardo rozpoczyna się, wybierając pozycję pierwszego elementu na liście. Oto, co się dzieje:

Po pierwsze został rozszerzony o interakcji między dwoma wzorników

![Interakcji](./media/azure-security-threat-modeling-tool-feature-overview/interaction.png)

Drugi, dodatkowych informacji na temat zagrożenia, który pojawia się w oknie właściwości przed zagrożeniami

![Informacje o interakcji](./media/azure-security-threat-modeling-tool-feature-overview/interactioninfo.png)

Wygenerowany zagrożeń pomaga mu zrozumienie potencjalnych wady projektowe. Kategoryzacji krok zapewnia, mu pomysł na potencjalnych ataków, podczas gdy dodatkowy opis informuje, jak dokładnie, czym jest to problem, oraz potencjalnych metody, aby go unikać. Służy on pola edytowalne sporządzać notatki w szczegółach uzasadnienie lub zmienić priorytet klasyfikacje, w zależności od paska usterkę w swojej organizacji.

Szablony platformy Azure mają dodatkowe szczegóły, aby pomóc użytkownikom w zrozumieniu, nie tylko na czym polega problem, ale także sposób rozwiązać ten problem, dodając opisy, przykłady i hiperłącza do dokumentacji dotyczącej platformy Azure.

Opis wprowadzonych mu weź pod uwagę znaczenie Dodawanie mechanizm uwierzytelniania, aby uniemożliwić użytkownikom fałszowania, odsłaniając pierwszy zagrożeń się opracowaniem. Kilka minut w dyskusji z Cristina, ich Zdajemy sobie sprawę Implementowanie kontroli dostępu i ról. Ricardo wypełnione kilka szybkich notatek do upewnij się, że zostały one zaimplementowane.

Jak Ricardo pojawiły się w zagrożeń w obszarze ujawnienie informacji, on zrealizowany planu kontroli dostępu, wymagane niektóre konta tylko do odczytu do inspekcji i Generowanie raportu. ADAM zastanawiasz się, czy powinna to być nowych zagrożeń, ale środki zaradcze są takie same, więc on odpowiednio oznaczone zagrożenia.
On również traktować o ujawnienie informacji nieco i zrealizowane, że taśm kopii zapasowych zostały będzie musiało jeszcze szyfrowania, zadania dla zespołu operacyjnego.

Zagrożenia, nie ma zastosowania do projektu z powodu istniejących środków zaradczych lub zabezpieczeń gwarantuje można ją zmienić na "Nie dotyczy" z listy rozwijanej stanu. Istnieją trzy inne opcje: Nierozpoczęte — domyślnie zaznaczoną opcję konieczne badanie — umożliwia śledzenie elementów i Mitigated — po w pełni są prace.

## <a name="reports--sharing"></a>Raporty i udostępnianie

Gdy Ricardo przechodzi przez listę Cristina i dodaje ważne uwagi, środki zaradcze/uzasadnienia, priorytet i stan zmieni się, wybiera he Raporty -> Utwórz pełny raport -> Zapisz raport, który wyświetla wygodnego raportu, jak przechodzić przez współpracownikom Zapewnij działanie zabezpieczeń jest implementowany.

![Informacje o interakcji](./media/azure-security-threat-modeling-tool-feature-overview/report.png)

Jeśli Ricardo chce, aby zamiast tego Udostępnij plik, on łatwo to zrobić przez zapisywanie na koncie usługi OneDrive w swojej organizacji. Po robi, i skopiuj link do dokumentu i udostępnić go swoim współpracownikom. 

## <a name="threat-modeling-meetings"></a>Spotkania modelowania zagrożeń

Gdy Ricardo jego model zagrożeń są wysyłane do swojego współpracownika za pomocą usługi OneDrive, Ashish, tester, został underwhelmed. Powiodło się, jak Ricardo i Cristina pominąć kilka ważnych narożnika przypadkach, które można łatwo złamać. Jego skepticism jest uzupełnieniem modele zagrożeń.

W tym scenariuszu po Ashish zajął stanowisko dyrektora model zagrożeń on wywołana dla dwóch spotkań modelowania zagrożeń: spotkania synchronizować procesu i przeprowadzenie diagramy, a następnie spotkanie drugi dla zagrożeń przeglądu i akceptacji.

W pierwszym spotkania Ashish poświęcony na 10 minut zalet wszystkim za pośrednictwem procesu modelowania zagrożeń SDL. Następnie on ściągane diagramu modelu zagrożeń i pracę, wyjaśniające, szczegółowo. W ciągu pięciu minut ważnym elementem Brak miał została zidentyfikowana.

Kilka minut później, Ashish i Ricardo stało się do rozszerzonego dyskusję na temat jak serwer sieci Web została skompilowana. Nie był idealny sposób na spotkanie kontynuować, ale wszyscy ostatecznie uzgodnione, wczesne wykrywanie rozbieżność został przejściem do zapisywania czas w przyszłości.

W drugim spotkania zespół tamtych zagrożenia, omówiono niektóre sposoby ich rozwiązywania i aprobuje model zagrożeń. Są sprawdzane dokumentu do kontroli źródła i kontynuując rozwój.

## <a name="thinking-about-assets"></a>Myśl o zasobach

Niektórzy czytelnicy posiadający zagrożeń modelowane zauważyć, że jeszcze nie wspomnieliśmy o zasobach na wszystkich. Odkryliśmy, wielu programistów lepiej niż ich zrozumienie pojęcia zasobów i jakie zasoby osoba atakująca może być zainteresowany zrozumieć ich oprogramowania.

Jeśli zamierzasz model zagrożeń domu, możesz zacząć krokiem jest wybranie swojej rodziny, wartościowych zdjęcia lub cenne kompozycji. Być może początek zastanawiać się, którzy mogą przestać działać w i bieżący system zabezpieczeń. Lub możesz zacząć, biorąc pod uwagę fizycznego funkcji, takich jak puli lub porch frontonu. Są one analogiczne do myśleć o zasoby, osoby atakujące lub projektowania oprogramowania. Działają dowolne z tych trzech metod.

Podejście do modelowania, które firma Microsoft została przedstawiona w tym miejscu zagrożenia jest znacznie prostsze niż firmy Microsoft zrobił w przeszłości. Stwierdziliśmy, że podejście do projektowania oprogramowania działa dobrze dla wielu zespołów. Mamy nadzieję, że zawierające należy do Ciebie.

## <a name="next-steps"></a>Następne kroki

Wyślij swoje pytania, komentarze i zagadnień związanych z tmtextsupport@microsoft.com. **[Pobierz](https://aka.ms/threatmodelingtool)**  narzędzie do modelowania zagrożeń na rozpoczęcie pracy.
