---
title: Kodowanie lub dekodowanie plików płaskich
description: Kodowanie lub dekodowanie plików płaskich w celu integracji przedsiębiorstwa z aplikacjami logiki Azure i pakietem integracji dla przedsiębiorstw
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 07/08/2016
ms.openlocfilehash: 2d6182ba01507c2fb361628e01bb52e1ea821f44
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77152656"
---
# <a name="encode-or-decode-flat-files-with-azure-logic-apps-and-enterprise-integration-pack"></a>Kodowanie lub dekodowanie plików płaskich za pomocą aplikacji Azure Logic Apps i pakietu Enterprise Integration Pack

Można zakodować zawartość XML przed wysłaniem go do partnera biznesowego w scenariuszu business-to-business (B2B). W aplikacji logiki można użyć łącznika kodowania plików płaskich, aby to zrobić. Utworzona przez ciebie aplikacja logiki może uzyskać zawartość XML z różnych źródeł, w tym z wyzwalacza żądania HTTP, z innej aplikacji, a nawet z jednego z wielu [łączników.](../connectors/apis-list.md) Aby uzyskać więcej informacji na temat aplikacji logiki, zobacz [dokumentację aplikacji logiki](logic-apps-overview.md "Dowiedz się więcej o aplikacjach logiki").  

## <a name="create-the-flat-file-encoding-connector"></a>Tworzenie łącznika kodowania plików płaskich
Wykonaj następujące kroki, aby dodać łącznik kodowania plików płaskich do aplikacji logiki.

1. Utwórz aplikację logiki i [połącz ją z kontem integracji](logic-apps-enterprise-integration-accounts.md "Dowiedz się, jak połączyć konto integracji z aplikacją Logika"). To konto zawiera schemat, który będzie używany do kodowania danych XML.  

1. W projektancie aplikacji logiki dodaj wyzwalacz **po odebraniu żądania HTTP** do aplikacji logiki.

1. Dodaj następującą akcję kodowania plików płaskich:

   a. Wybierz znak **plus.**

   b. Wybierz łącze **Dodaj akcję** (pojawia się po wybraniu znaku plus).

   d. W polu wyszukiwania wprowadź *pole mieszkanie,* aby filtrować wszystkie akcje do tej, której chcesz użyć.

   d. Wybierz opcję **Kodowanie płaskich plików** z listy.   

      ![Zrzut ekranu przedstawiający opcję Kodowania plików płaskich](media/logic-apps-enterprise-integration-flatfile/flatfile-2.png)   

1. W oknie dialogowym **Kodowanie płaskich plików** zaznacz pole **tekstowe Zawartość.**  

   ![Zrzut ekranu przedstawiający pole tekstowe Zawartość](media/logic-apps-enterprise-integration-flatfile/flatfile-3.png)  

1. Wybierz znacznik treści jako zawartość, którą chcesz zakodować. Znacznik treści wypełni pole zawartości.     

   ![Zrzut ekranu przedstawiający znacznik treści](media/logic-apps-enterprise-integration-flatfile/flatfile-4.png)  

1. Zaznacz pole listy **Nazwa schematu** i wybierz schemat, którego chcesz użyć do zakodowania zawartości wejściowej.    

   ![Zrzut ekranu przedstawiający pole listy Nazwa schematu](media/logic-apps-enterprise-integration-flatfile/flatfile-5.png)  

1. Zapisz pracę.

   ![Zrzut ekranu przedstawiający ikonę Zapisz](media/logic-apps-enterprise-integration-flatfile/flatfile-6.png)  

W tym momencie zakończono konfigurowanie łącznika kodowania plików płaskich. W aplikacji w świecie rzeczywistym można zapisać zakodowane dane w aplikacji biznesowej, takiej jak Salesforce. Możesz też wysłać te zakodowane dane do partnera handlowego. Możesz łatwo dodać akcję, aby wysłać dane wyjściowe akcji kodowania do Salesforce lub do partnera handlowego, używając jednego z innych dostarczonych łączników.

Teraz można przetestować łącznika, składając żądanie do punktu końcowego HTTP i uwzględniając zawartość XML w treści żądania.  

## <a name="create-the-flat-file-decoding-connector"></a>Tworzenie łącznika dekodowania pliku płaskiego

> [!NOTE]
> Aby wykonać te kroki, musisz mieć plik schematu już przekazany do konta integracji.

1. W projektancie aplikacji logiki dodaj wyzwalacz **po odebraniu żądania HTTP** do aplikacji logiki.

1. Dodaj akcję dekodowania pliku płaskiego w następujący sposób:

   a. Wybierz znak **plus.**

   b. Wybierz łącze **Dodaj akcję** (pojawia się po wybraniu znaku plus).

   d. W polu wyszukiwania wprowadź *pole mieszkanie,* aby filtrować wszystkie akcje do tej, której chcesz użyć.

   d. Wybierz opcję **Dekodowanie płaskiego pliku** z listy.   

      ![Zrzut ekranu przedstawiający opcję Dekodowania pliku płaskiego](media/logic-apps-enterprise-integration-flatfile/flatfile-2.png)   

1. Wybierz **kontrolka Zawartość.** Spowoduje to wyświetlenie listy zawartości z wcześniejszych kroków, których można użyć jako zawartości do dekodowania. Należy zauważyć, że *treść* z przychodzącego żądania HTTP jest dostępna do użycia jako zawartość do dekodowania. Można również wprowadzić zawartość, aby dekodować bezpośrednio do **content** formant.     

1. Wybierz znacznik *Ciało.* Zwróć uwagę, że znacznik treści znajduje się teraz w formancie **Zawartość.**

1. Wybierz nazwę schematu, który ma być używany do dekodowania zawartości. Poniższy zrzut ekranu pokazuje, że *OrderFile* jest nazwą wybranego schematu. Ta nazwa schematu została wcześniej przekazana do konta integracji.

   ![Zrzut ekranu przedstawiający okno dialogowe Dekodowanie plików płaskich](media/logic-apps-enterprise-integration-flatfile/flatfile-decode-1.png) 

1. Zapisz pracę.  

   ![Zrzut ekranu przedstawiający ikonę Zapisz](media/logic-apps-enterprise-integration-flatfile/flatfile-6.png)    

W tym momencie zakończono konfigurowanie łącznika dekodowania plików płaskich. W aplikacji w świecie rzeczywistym można przechowywać zdekodowane dane w aplikacji biznesowej, takiej jak Salesforce. Można łatwo dodać akcję, aby wysłać dane wyjściowe akcji dekodowania do Salesforce.

Teraz można przetestować łącznika, składając żądanie do punktu końcowego HTTP i dołączając zawartość XML, którą chcesz zdekodować w treści żądania.  

## <a name="next-steps"></a>Następne kroki
* [Dowiedz się więcej o pakiecie Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md "Dowiedz się więcej o pakiecie integracji w przedsiębiorstwie").  

