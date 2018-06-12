---
title: Kodowania i dekodowania plików prostych w aplikacjach logiki platformy Azure | Dokumentacja firmy Microsoft
description: Jak używać pliku plik encoder lub dekodera w pakiet integracyjny dla przedsiębiorstw w aplikacjach logiki
services: logic-apps
documentationcenter: .net,nodejs,java
author: divyaswarnkar
manager: jeconnoc
editor: ''
ms.assetid: 82152dab-c7ad-43df-b721-596559703be8
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/08/2016
ms.author: LADocs; divswa
ms.openlocfilehash: 2f88b9c3d25b1a4771772e32bac8a4f7893831cf
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/11/2018
ms.locfileid: "35300083"
---
# <a name="overview-of-enterprise-integration-with-flat-files"></a>Omówienie integracji przedsiębiorstwa z plików prostych

Warto kodowania zawartości XML przed wysłaniem do partnerom biznesowym, w przypadku biznesowych między firmami (B2B). W aplikacji logiki można użyć pliku prostego kodowanie łącznika, w tym celu. Tworzonej aplikacji logiki można uzyskać jego XML zawartości z różnych źródeł, takich jak wyzwalacz żądania HTTP, z innej aplikacji lub nawet z jednego z wielu [łączniki](../connectors/apis-list.md). Aby uzyskać więcej informacji na temat aplikacji logiki, zobacz [dokumentacji aplikacje logiki](logic-apps-overview.md "Dowiedz się więcej o aplikacjach Logic apps").  

## <a name="create-the-flat-file-encoding-connector"></a>Tworzenie pliku prostego kodowanie łącznika
Wykonaj następujące kroki, aby dodać plik prosty kodowanie łącznika do aplikacji logiki.

1. Tworzenie aplikacji logiki i [połączyć go z kontem integracji](logic-apps-enterprise-integration-accounts.md "Dowiedz się połączyć konto integracji aplikacji logiki"). To konto zawiera schemat, który będzie używany do kodowania danych XML.  
2. Dodaj **żądania - zostanie odebrane żądanie HTTP podczas** do aplikacji logiki wyzwalacza.  
   ![Zrzut ekranu przedstawiający wyzwalacza, aby wybrać](./media/logic-apps-enterprise-integration-b2b/flatfile-1.png)    
3. Dodawanie pliku prostego kodowanie akcji, w następujący sposób:
   
    a. Wybierz **plus** logowania.
   
    b. Wybierz **Dodaj akcję** łącza (pojawia się po wybraniu znak plus).
   
    c. W polu wyszukiwania wprowadź *płaskiej* do filtrowania wszystkie akcje do tego, który ma być używany.
   
    d. Wybierz **płaskiej kodowanie pliku** opcję z listy.   
   ![Opcja Zrzut ekranu z płaskim kodowanie pliku](media/logic-apps-enterprise-integration-flatfile/flatfile-2.png)   
4. Na **płaskiej kodowanie pliku** okno dialogowe, wybierz opcję **zawartości** pola tekstowego.  
   ![Zrzut ekranu zawartości pola tekstowego](media/logic-apps-enterprise-integration-flatfile/flatfile-3.png)  
5. Zaznacz tag treści jako zawartości, który chcesz kodować. Tag treści będzie wypełnić pole zawartości.     
   ![Zrzut ekranu przedstawiający znacznik treści](media/logic-apps-enterprise-integration-flatfile/flatfile-4.png)  
6. Wybierz **nazwy schematu** pola listy, a następnie wybierz pozycję schematu ma być używany do kodowania zawartości wejściowej.    
   ![Zrzut ekranu Nazwa schematu pola listy](media/logic-apps-enterprise-integration-flatfile/flatfile-5.png)  
7. Zapisz pracę.   
   ![Zrzut ekranu zapisać ikony](media/logic-apps-enterprise-integration-flatfile/flatfile-6.png)  

W tym momencie po zakończeniu konfigurowania łącznika kodowania z pliku prostego. W przypadku aplikacji rzeczywistych można przechowywać dane zakodowane w aplikacji — biznesowych, takich jak Salesforce. Lub możesz wysłać, że dane zakodowane do obrotu partnera. Możesz łatwo dodać akcję w celu wysyłania danych wyjściowych akcji kodowania Salesforce lub partnerem handlowym przy użyciu dowolnego z łączników, pod warunkiem.

Teraz możesz przetestować Twojego łącznika przesyłania do punktu końcowego HTTP, a tym zawartości XML w treści żądania.  

## <a name="create-the-flat-file-decoding-connector"></a>Tworzenie pliku prostego dekodowania łącznika

> [!NOTE]
> Do wykonania tych czynności, należy mieć plik schematu już przekazany do konta integracji.

1. Dodaj **żądania - zostanie odebrane żądanie HTTP podczas** do aplikacji logiki wyzwalacza.  
   ![Zrzut ekranu przedstawiający wyzwalacza, aby wybrać](./media/logic-apps-enterprise-integration-b2b/flatfile-1.png)    
2. Dodawanie pliku prostego dekodowania akcji, w następujący sposób:
   
    a. Wybierz **plus** logowania.
   
    b. Wybierz **Dodaj akcję** łącza (pojawia się po wybraniu znak plus).
   
    c. W polu wyszukiwania wprowadź *płaskiej* do filtrowania wszystkie akcje do tego, który ma być używany.
   
    d. Wybierz **płaskiej dekodowania pliku** opcję z listy.   
   ![Zrzut ekranu z płaskim pliku dekodowania opcji](media/logic-apps-enterprise-integration-flatfile/flatfile-2.png)   
3. Wybierz **zawartości** formantu. To tworzy listę zawartości z wcześniejszych krokach, których można użyć jako zawartości zdekodować. Zwróć uwagę, że *treści* z przychodzącego protokołu HTTP jest dostępny do użycia jako zawartości w celu zdekodowania żądania. Możesz też wprowadzić zawartości w celu zdekodowania bezpośrednio do **zawartości** formantu.     
4. Wybierz *treści* tagu. Powiadomienie tag treści jest teraz w **zawartości** formantu.
5. Wybierz nazwę schematu, do którego chcesz użyć do zdekodowania. Poniższy zrzut ekranu pokazuje, że *OrderFile* to nazwa wybranego schematu. Ta nazwa schematu była wcześniej przekazany pod uwagę integracji.
   
   ![Zrzut ekranu z płaskim pliku dekodowania — okno dialogowe](media/logic-apps-enterprise-integration-flatfile/flatfile-decode-1.png)    
6. Zapisz pracę.  
   ![Zrzut ekranu zapisać ikony](media/logic-apps-enterprise-integration-flatfile/flatfile-6.png)    

W tym momencie po zakończeniu konfigurowania pliku płaskiej dekodowania łącznika. W przypadku aplikacji rzeczywistych można przechowywać dekodowane dane w aplikacji — biznesowych, takich jak Salesforce. Możesz łatwo dodać akcję do wysyłania danych wyjściowych dekodowania akcji do usług Salesforce.

Teraz możesz przetestować Twojego łącznika żądanie do punktu końcowego HTTP i tym zawartość XML, który ma zostać zdekodowany w treści żądania.  

## <a name="next-steps"></a>Kolejne kroki
* [Dowiedz się więcej o pakiet integracyjny dla przedsiębiorstw](logic-apps-enterprise-integration-overview.md "Dowiedz się więcej na temat pakiet integracyjny dla przedsiębiorstw").  

