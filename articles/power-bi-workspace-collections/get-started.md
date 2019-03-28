---
title: Wprowadzenie do kolekcji obszarów roboczych usługi Microsoft Power BI
description: Kolekcje obszarów roboczych usługi Power BI to usługa platformy Azure, która umożliwia programistom dodawanie interaktywnych raportów usługi Power BI do własnych aplikacji.
services: power-bi-workspace-collections
ms.service: power-bi-workspace-collections
author: markingmyname
ms.author: maghan
ms.topic: conceptual
ms.workload: powerbi
ms.date: 09/25/2017
ms.openlocfilehash: 751d69cd9b52cf88bb54900e67283fdff3d90579
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2019
ms.locfileid: "58520483"
---
# <a name="get-started-with-microsoft-power-bi-workspace-collections"></a>Wprowadzenie do kolekcji obszarów roboczych usługi Microsoft Power BI

**Kolekcje obszarów roboczych usługi Power BI** to usługa platformy Azure, która umożliwia programistom dodawanie interaktywnych raportów usługi Power BI do własnych aplikacji. **Kolekcje obszarów roboczych usługi Power BI** działają z istniejącymi aplikacjami bez potrzeby ich przeprojektowywania lub zmiany sposobu logowania się przez użytkowników.

> [!IMPORTANT]
> Kolekcje obszarów roboczych usługi Power BI są przestarzałe i będą dostępne do czerwca 2018 roku lub do daty podanej w kontrakcie. Zachęcamy do zaplanowania migracji do usługi Power BI Embedded, aby uniknąć przerw w działaniu aplikacji. Aby uzyskać informacje dotyczące sposobu przeprowadzenia migracji danych do usługi Power BI Embedded, zobacz [How to migrate Power BI Workspace Collections content to Power BI Embedded (Migrowanie zawartości kolekcji obszarów roboczych usługi Power BI do usługi Power BI Embedded)](https://powerbi.microsoft.com/documentation/powerbi-developer-migrate-from-powerbi-embedded/).

Zasoby dla **kolekcji obszarów roboczych usługi Microsoft Power BI** są aprowizowane za pośrednictwem [interfejsów API usługi Azure Resource Manager](https://msdn.microsoft.com/library/mt712306.aspx). W tym przypadku aprowizowanym zasobem jest **kolekcja obszarów roboczych usługi Power BI**.

![Ogólny przepływ kolekcji obszarów roboczych usługi Power BI](media/get-started/introduction.png)

## <a name="create-a-workspace-collection"></a>Tworzenie kolekcji obszarów roboczych

**Kolekcja obszarów roboczych** jest najwyższego poziomu zasobem platformy Azure i kontenerem zawartości, która zostanie osadzona w aplikacji. **Kolekcję obszarów roboczych** można utworzyć na dwa sposoby:

* Ręcznie przy użyciu witryny Azure Portal
* Programowo przy użyciu interfejsów API usługi Azure Resource Manager

Przejdźmy do kroków tworzenia **kolekcji obszarów roboczych** przy użyciu witryny Azure Portal.

1. Otwórz witrynę **Azure Portal** i zaloguj się: [https://portal.azure.com](https://portal.azure.com).
2. Wybierz pozycję **+ Nowy** na górnym panelu.
   
   ![Pozycja + Nowy w witrynie Azure Portal](media/get-started/create-workspace-1.png)
3. W obszarze **Dane i analiza** wybierz pozycję **Kolekcja obszarów roboczych usługi Power BI**.
4. W komunikacie początkowym, jeśli masz już istniejącą subskrypcję kolekcji obszarów roboczych usługi Power BI, wybierz pozycję **Utwórz kolekcję obszarów roboczych** u dołu.

5. Na stronie **Kolekcja obszarów roboczych** wprowadź wymagane informacje.
   
   ![Tworzenie kolekcji obszarów roboczych](media/get-started/create-workspace-2.png)
1. Wybierz pozycję **Utwórz**.

Aprowizacja **Kolekcji obszarów roboczych** potrwa kilka chwil. Po zakończeniu nastąpi przekierowanie do **Kolekcji obszarów roboczych**.

   ![Kolekcja obszarów roboczych w witrynie Azure Portal](media/get-started/create-workspace-3.png)

Wyniki **Tworzenia** zawierają informacje potrzebne do wywoływania interfejsów API służących do tworzenia obszarów roboczych i wdrażania do nich zawartości.

<a name="view-access-keys"/>

## <a name="view-power-bi-api-access-keys"></a>Wyświetlanie kluczy dostępu interfejsu API usługi Power BI

Jedną z najważniejszych informacji potrzebnych do wywoływania interfejsów API REST usługi Power BI są **klucze dostępu**. Są one używane do generowania **tokenów aplikacji** służących do uwierzytelniania żądań interfejsu API. Aby wyświetlić **Klucze dostępu**, kliknij przycisk **Klucze dostępu** w obszarze **Ustawienia**. Aby uzyskać więcej informacji o **tokenach aplikacji**, zobacz [Authenticating and authorizing with Power BI Workspace Collections (Uwierzytelnianie i autoryzowanie w kolekcji obszarów roboczych usługi Power BI)](app-token-flow.md).

   ![Klucze dostępu w ramach ustawień kolekcji obszarów roboczych w witrynie Azure Portal](media/get-started/access-keys.png)

Zauważysz, że masz dwa klucze.

   ![Dwa klucze w obszarze Klucze dostępu](media/get-started/access-keys-2.png)

Skopiuj te klucze i przechowuj je bezpiecznie w aplikacji. Ważne jest, aby traktować te klucze jak hasła, ponieważ zapewniają one dostęp do całej zawartości Twojej **kolekcji obszarów roboczych**.

Chociaż wyświetlane są dwa klucze, w określonym czasie potrzebny jest tylko jeden. Drugi klucz jest dostarczany, aby można było okresowo generować ponownie klucze bez przerywania dostępu do usługi.

Teraz, gdy masz wystąpienie usługi Power BI dla danej aplikacji oraz **klucze dostępu**, możesz zaimportować raport do aplikacji. Przed zapoznaniem się z instrukcją importowania raportu przeczytaj informacje o tworzeniu zestawów danych i raportów usługi Power BI w celu osadzenia w aplikacji zawarte w następnej części.

## <a name="working-with-workspaces"></a>Praca z obszarami roboczymi

Po utworzeniu kolekcji obszarów roboczych musisz utworzyć obszar roboczy, który będzie przechowywać Twoje raporty i zestawy danych. Do utworzenia obszaru roboczego musisz użyć [interfejsu API REST publikowania obszaru roboczego](https://msdn.microsoft.com/library/azure/mt711503.aspx).

## <a name="create-power-bi-datasets-and-reports-to-embed-into-an-app-using-power-bi-desktop"></a>Tworzenie zestawów danych i raportów usługi Power BI w celu osadzenia w aplikacji za pomocą programu Power BI Desktop

Teraz, gdy masz utworzone wystąpienie usługi Power BI dla aplikacji i **klucze dostępu**, musisz utworzyć zestawy danych i raporty usługi Power BI, które mają zostać osadzone. Zestawy danych i raporty można tworzyć za pomocą programu **Power BI Desktop**. Możesz pobrać program [Power BI Desktop za darmo](https://go.microsoft.com/fwlink/?LinkId=521662). Możesz też pobrać plik z [przykładem analizy sprzedaży PBIX](https://go.microsoft.com/fwlink/?LinkID=780547), aby szybko rozpocząć pracę.

> [!NOTE]
> Aby dowiedzieć się więcej o sposobie używania programu **Power BI Desktop**, zobacz stronę [Getting Started With Power BI Desktop](https://powerbi.microsoft.com/guided-learning/powerbi-learning-0-2-get-started-power-bi-desktop) (Wprowadzenie do programu Power BI Desktop).

W programie **Power BI Desktop** łączysz się ze źródłem danych, importując kopię danych do programu **Power BI Desktop**, lub korzystasz bezpośrednio ze źródła danych przy użyciu **trybu DirectQuery**.

Poniżej przedstawiono różnice między **importem** a **trybem DirectQuery**.

| Import | Tryb DirectQuery |
| --- | --- |
| Tabele, kolumny, *i dane* są importowane lub kopiowane do programu **Power BI Desktop**. Podczas pracy nad wizualizacjami program **Power BI Desktop** wykonuje zapytania na kopii danych. Aby zobaczyć wszelkie zmiany, które nastąpiły w danych źródłowych, trzeba odświeżyć lub ponownie zaimportować pełny bieżący zestaw danych. |Tylko *tabele i kolumny* są importowane lub kopiowane do programu **Power BI Desktop**. Podczas pracy nad wizualizacjami program **Power BI Desktop** wysyła zapytania do oryginalnego źródła danych, więc widoczne dane są zawsze aktualne. |

Aby poznać szczegółowe informacje na temat nawiązywania połączenia ze źródłem danych, zobacz temat [Nawiązywanie połączenia ze źródłem danych](connect-datasource.md).

Po zapisaniu pracy w programie **Power BI Desktop** tworzony jest plik PBIX. Ten plik zawiera raport. Ponadto w przypadku importowania danych plik PBIX zawiera pełen zestaw danych. Jeśli natomiast używasz **trybu DirectQuery**, plik PBIX zawiera tylko schemat zestawu danych. Programowe wdrażanie pliku PBIX do obszaru roboczego przy użyciu interfejsu [API Import usługi Power BI](https://msdn.microsoft.com/library/mt711504.aspx).

> [!NOTE]
> **Kolekcje obszarów roboczych usługi Power BI** mają dodatkowe interfejsy API, które pozwalają zmienić serwer i bazę danych wskazywane przez zestaw danych, a także ustawić poświadczenia konta usługi używane przez zestaw danych do łączenia się z bazą danych. Zobacz artykuły dotyczące operacji [Post SetAllConnections](https://msdn.microsoft.com/library/mt711505.aspx) i [Patch Gateway Datasource](https://msdn.microsoft.com/library/mt711498.aspx).

## <a name="create-power-bi-datasets-and-reports-using-apis"></a>Tworzenie zestawów danych i raportów usługi Power BI za pomocą interfejsów API

### <a name="datasets"></a>Zestawy danych

Zestawy danych w kolekcjach obszarów roboczych usługi Power BI możesz tworzyć przy użyciu interfejsu API REST. Następnie można wypchnąć dane do własnego zestawu danych. Pozwala to na pracę z danymi bez konieczności używania programu Power BI Desktop. Aby uzyskać więcej informacji, zobacz: [Post Datasets](https://msdn.microsoft.com/library/azure/mt778875.aspx) (Wysyłanie zestawów danych za pomocą żądania POST).

### <a name="reports"></a>Raporty

Raport z zestawu danych można utworzyć bezpośrednio w aplikacji za pomocą interfejsu API języka JavaScript. Aby uzyskać więcej informacji, zobacz [Create a new report from a dataset in Power BI Workspace Collections (Tworzenie nowego raportu z zestawu danych w kolekcjach obszarów roboczych usługi Power BI)](create-report-from-dataset.md).

## <a name="see-also"></a>Zobacz też

[Rozpoczęcie pracy z przykładem](get-started-sample.md)  
[Authenticating and authorizing with Power BI Workspace Collections (Uwierzytelnianie i autoryzowanie za pomocą kolekcji obszarów roboczych usługi Power BI)](app-token-flow.md)  
[Embed a report](embed-report.md) (Osadzanie raportu)  
[Create a new report from a dataset in Power BI Workspace Collections (Tworzenie nowego raportu z zestawu danych w kolekcjach obszarów roboczych usługi Power BI)](create-report-from-dataset.md)
[Zapisywanie raportów](save-reports.md)  
[Program Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-get-the-desktop/)  
[Przykład osadzania skryptu JavaScript](https://microsoft.github.io/PowerBI-JavaScript/demo/)  

Masz więcej pytań? [Dołącz do społeczności użytkowników usługi Power BI](https://community.powerbi.com/)

