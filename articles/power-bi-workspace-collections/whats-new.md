---
title: Co nowego w kolekcjach obszarów roboczych usługi Power BI
description: Uzyskaj najnowsze informacje o nowościach w kolekcji obszarów roboczych usługi Power BI
services: power-bi-workspace-collections
ms.service: power-bi-workspace-collections
author: rkarlin
ms.author: rkarlin
ms.topic: article
ms.workload: powerbi
ms.date: 09/20/2017
ms.openlocfilehash: 9ef9416abb4aaf0927d1e072286ef83a4d8c39cb
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "64724017"
---
# <a name="whats-new-in-power-bi-workspace-collections"></a>Co nowego w kolekcjach obszarów roboczych usługi Power BI

Aktualizacje **kolekcji obszarów roboczych usługi Power BI** są wydawane w regularnych odstępach czasu. Jednak nie każda wersja zawiera nowe funkcje widocznych dla użytkownika; Niektóre wersje koncentrują się na możliwości usługi zaplecza. Możemy wyróżnić nowe możliwości użytkownika w tym miejscu.

> [!IMPORTANT]
> Kolekcje obszarów roboczych usługi Power BI są przestarzałe i będą dostępne do czerwca 2018 roku lub do daty podanej w kontrakcie. Zachęcamy do zaplanowania migracji do usługi Power BI Embedded, aby uniknąć przerw w działaniu aplikacji. Aby uzyskać informacje dotyczące sposobu przeprowadzenia migracji danych do usługi Power BI Embedded, zobacz [How to migrate Power BI Workspace Collections content to Power BI Embedded (Migrowanie zawartości kolekcji obszarów roboczych usługi Power BI do usługi Power BI Embedded)](https://powerbi.microsoft.com/documentation/powerbi-developer-migrate-from-powerbi-embedded/).

## <a name="march-2017"></a>Marca 2017 r.

**Usługi możliwości samoobsługi**

* [Utwórz nowy raport](create-report-from-dataset.md)
* [Zapisz jako raport](save-reports.md)
* Osadzanie raportu w nowym trybie Odczyt/edytowania/tworzenia 
* [Raport Przełącz między trybem edycji/Odczyt](toggle-mode.md)

**Połączenia z danymi za pomocą interfejsów API REST**

* [Tworzenie zestawu danych](https://msdn.microsoft.com/library/azure/mt778875.aspx)
* Wypychanie danych 

**Interfejsy API Management**

* Klonuj raport i zestaw danych
* Powiąż raport z innego zestawu danych

**Przykłady**

* Zaktualizowano [przykład osadzania przy użyciu języka JavaScript raport](https://microsoft.github.io/PowerBI-JavaScript/demo)

## <a name="december-2016"></a>Grudnia 2016 r.

* [Przykład osadzania przy użyciu nowego języka JavaScript](https://microsoft.github.io/PowerBI-JavaScript/demo/)

## <a name="october-2016"></a>Października 2016 r.

* [Zaawansowane funkcje analityczne przy użyciu kolekcji usługi Power BI w obszarze roboczym i R](https://powerbi.microsoft.com/blog/r-in-pbie/)

## <a name="august-31-2016"></a>31 sierpnia 2016 r.
Zawarte w tej wersji:

* Wszystkie nowe zestaw SDK języka JavaScript, która obsługuje [zaawansowane filtrowanie i nawigowania po stronach](interact-with-reports.md).
* Kolekcje obszarów roboczych usługi Power BI są teraz obsługiwane w Kanada Środkowa centrum danych. Sprawdź [centrum danych stanie](https://azure.microsoft.com/status/).

## <a name="july-11-2016"></a>11 lipca 2016 r.
Zawarte w tej wersji:

* **Świetnie!** Usługa kolekcje obszarów roboczych usługi Power BI nie jest już w wersji zapoznawczej — jego teraz wersji ogólnie dostępnej (dostępne ogólnie).  
* Wszystkie interfejsy API REST zostały przeniesione z **/beta** do **/v1.0**.
* .NET i języka JavaScript SDK został zaktualizowany dla **v1.0**.
* Usługa Power BI interfejsu API mogą być uwierzytelniane teraz bezpośrednio przy użyciu kluczy interfejsu API. Tokenów aplikacji są wymagane tylko w przypadku osadzania. W ramach tego tokenów aprowizacji i deweloperów zostały zaniechane w interfejsów API w wersji 1.0, ale będą one nadal działać w wersji beta do 30 grudnia 2016 r. Aby dowiedzieć się więcej, zobacz [uwierzytelnianie i autoryzowanie za pomocą kolekcji obszarów roboczych usługi Power BI](app-token-flow.md).
* Wiersza (RLS) zabezpieczenia na poziomie obsługę tokenów aplikacji i osadzone raporty. Aby dowiedzieć się więcej, zobacz [zabezpieczeń z kolekcji obszarów roboczych usługi Power BI na poziomie wiersza](row-level-security.md).
* Zaktualizowana Przykładowa aplikacja dla wszystkich **v1.0** wywołań interfejsu API.
* Kolekcje obszarów roboczych usługi Power BI obsługuje zestaw SDK usługi Azure, programu PowerShell i interfejsu wiersza polecenia.
* Użytkownicy mogą eksportować dane wizualizacji **CSV**.
* Kolekcje obszarów roboczych usługi Power BI są teraz obsługiwane w przypadku wszystkich tych samych języków/ustawień regionalnych jako Microsoft Azure. Aby dowiedzieć się więcej, zobacz [platformy Azure — języki](https://social.technet.microsoft.com/wiki/contents/articles/4234.windows-azure-extent-of-localization.aspx).

