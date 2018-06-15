---
title: Zarządzanie kluczami w LUIS | Dokumentacja firmy Microsoft
description: Opis języka (LUIS) umożliwia zarządzanie programowe interfejsu API, punktu końcowego i klucze zewnętrzne.
titleSuffix: Azure
services: cognitive-services
author: v-geberr
manager: Kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 03/21/2018
ms.author: v-geberr
ms.openlocfilehash: b1d0d01621769a6bfc0e14e703d7f988403703c6
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "35349781"
---
# <a name="manage-your-luis-keys"></a>Zarządzanie kluczami LUIS
Klucz umożliwia tworzenie i publikowanie aplikacji LUIS lub zapytania punktu końcowego. 

<a name="programmatic-key" ></a>
<a name="authoring-key" ></a>
<a name="endpoint-key" ></a>
<a name="use-endpoint-key-in-query" ></a>
<a name="api-usage-of-ocp-apim-subscription-key" ></a>
<a name="key-limits" ></a>
<a name="key-limit-errors" ></a>
## <a name="key-concepts"></a>Kluczowe pojęcia
Zobacz [kluczy w LUIS](luis-concept-keys.md) LUIS do tworzenia i punktu końcowego kluczowych założeń zrozumienie.

<a name="create-and-use-an-endpoint-key"></a>
## <a name="assign-endpoint-key"></a>Przypisz klucz punktu końcowego
Na **publikowania aplikacji** pozycję istnieje już klucz w **zasobów i klucze** tabeli. Jest to tworzenia klucza (początkową). 

1. Utwórz klucz LUIS na [portalu Azure](https://portal.azure.com). Aby uzyskać dalsze instrukcje, zobacz [Tworzenie klucza subskrypcji przy użyciu usługi Azure](luis-how-to-azure-subscription.md).
 
2. Aby dodać klucz LUIS utworzony w poprzednim kroku, kliknij przycisk **Dodaj klucz** przycisk, aby otworzyć **przypisać klawisz aplikacji** okna dialogowego. 

    ![Przypisz klucz do aplikacji](./media/luis-manage-keys/assign-key.png)
3. W oknie dialogowym Wybierz dzierżawcy. 
 
    > [!Note]
    > Na platformie Azure dzierżawca reprezentuje Azure Active Directory identyfikator klienta lub organizacja skojarzonego z usługą. Jeśli użytkownik wcześniej zapisany subskrypcji platformy Azure za pomocą indywidualnego Account firmy Microsoft, masz już dzierżawę! Po zalogowaniu się do portalu Azure, użytkownik jest automatycznie zalogowany do [dzierżawy domyślne](https://docs.microsoft.com/azure/active-directory/develop/active-directory-howto-tenant). Możesz mogą korzystać z tej dzierżawy, ale warto utworzyć konto administratora organizacyjnego.

4. Wybierz subskrypcję platformy Azure skojarzoną z kluczem Azure LUIS, którą chcesz dodać.

5. Wybierz konto Azure LUIS. Region konta są wyświetlane w nawiasach. 

    ![Wybierz klucz](./media/luis-manage-keys/assign-key-filled-out.png)

6. Po przypisaniu tego klucza punktu końcowego, należy jej używać w wszystkie zapytania punktu końcowego. 

<!-- content moved to luis-reference-regions.md, need replacement links-->
<a name="regions-and-keys"></a>
<a name="publishing-to-europe"></a>
<a name="publishing-to-australia"></a>

## <a name="publishing-regions"></a>Regiony publikowania
Dowiedz się więcej na temat publikowania [regionów](luis-reference-regions.md) publikowania w tym [Europy](luis-reference-regions.md#publishing-to-europe), i [Australii](luis-reference-regions.md#publishing-to-australia). Regiony publikowania różnią się od tworzenia regionów. Upewnij się, że tworzenie aplikacji w polu tworzenia region do publikowania żądany region.

## <a name="unassign-key"></a>Przypisanie klucza

* W **listy zasobów i klucze**, kliknij ikonę Kosza bin obok jednostki chcesz cofnąć przypisanie. Następnie kliknij przycisk **OK** w komunikacie potwierdzenia, aby potwierdzić usunięcie.
 
    ![Przypisanie jednostek](./media/luis-manage-keys/unassign-key.png)

> [!NOTE]
> Cofania przypisania klucza LUIS nie powoduje jego usunięcia z subskrypcją platformy Azure.

## <a name="next-steps"></a>Kolejne kroki

Publikowanie aplikacji w przy użyciu klucza **publikowania aplikacji** strony. Aby uzyskać instrukcje dotyczące publikowania, zobacz [publikowania aplikacji](PublishApp.md).

[LUIS]: luis-reference-regions.md#luis-website