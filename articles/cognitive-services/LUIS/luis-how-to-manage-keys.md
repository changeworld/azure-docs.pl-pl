---
title: Zarządzanie kluczami w LUIS | Dokumentacja firmy Microsoft
description: Language Understanding (LUIS) umożliwia zarządzanie programowego interfejsu API, punktu końcowego i klucze zewnętrzne.
titleSuffix: Azure
services: cognitive-services
author: v-geberr
manager: Kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 03/21/2018
ms.author: v-geberr
ms.openlocfilehash: 99ba867b10c82d226948b13fda448cd24a33c553
ms.sourcegitcommit: 11321f26df5fb047dac5d15e0435fce6c4fde663
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/06/2018
ms.locfileid: "37888732"
---
# <a name="manage-your-luis-keys"></a>Zarządzanie kluczami usługi LUIS
Klucz umożliwia tworzenie i publikowanie aplikacji usługi LUIS lub kwerendy punktu końcowego usługi. 

<a name="programmatic-key" ></a>
<a name="authoring-key" ></a>
<a name="endpoint-key" ></a>
<a name="use-endpoint-key-in-query" ></a>
<a name="api-usage-of-ocp-apim-subscription-key" ></a>
<a name="key-limits" ></a>
<a name="key-limit-errors" ></a>
## <a name="key-concepts"></a>Kluczowe pojęcia
Zobacz [kluczy w LUIS](luis-concept-keys.md) zrozumienie usługi LUIS do tworzenia i punktu końcowego kluczowych pojęć.

<a name="create-and-use-an-endpoint-key"></a>
## <a name="assign-endpoint-key"></a>Przypisz klucza punktu końcowego
Na **Opublikuj aplikację** stronie znajduje się już klucz w **zasobów i klucze** tabeli. Jest to tworzenia klucza (starter). 

1. Utwórz klucz usługi LUIS w [witryny Azure portal](https://portal.azure.com). Aby uzyskać dalsze instrukcje, zobacz [tworzenia klucza punktu końcowego przy użyciu platformy Azure](luis-how-to-azure-subscription.md).
 
2. Aby dodać klucz usługi LUIS, utworzony w poprzednim kroku, kliknij przycisk **Dodaj klucz** przycisk, aby otworzyć **przypisać klawisz do swojej aplikacji** okna dialogowego. 

    ![Przypisać klawisz do aplikacji](./media/luis-manage-keys/assign-key.png)
3. Wybierz dzierżawę w oknie dialogowym. 
 
    > [!Note]
    > Na platformie Azure dzierżawca reprezentuje identyfikator Azure Active Directory klienta lub organizacji skojarzonego z usługą. Jeśli użytkownik wcześniej zarejestrowane w celu uzyskania subskrypcji platformy Azure za pomocą indywidualnego Account firmy Microsoft, masz już dzierżawę. Po zalogowaniu się do witryny Azure portal automatycznie zalogujesz się do [domyślnej dzierżawy](https://docs.microsoft.com/azure/active-directory/develop/active-directory-howto-tenant). Należy mogą korzystać z tej dzierżawy, ale warto utworzyć konto administratora organizacyjnego.

4. Wybierz subskrypcję platformy Azure skojarzoną z kluczem usługi LUIS platformy Azure, który chcesz dodać.

5. Wybierz konto usługi LUIS platformy Azure. Region konta jest wyświetlany w nawiasach. 

    ![Naciśnij klawisz](./media/luis-manage-keys/assign-key-filled-out.png)

6. Po przypisaniu tego klucza punktu końcowego, należy jej używać w wszystkie zapytania punktu końcowego. 

<!-- content moved to luis-reference-regions.md, need replacement links-->
<a name="regions-and-keys"></a>
<a name="publishing-to-europe"></a>
<a name="publishing-to-australia"></a>

## <a name="publishing-regions"></a>Publikowanie regionów
Dowiedz się więcej o publikowaniu [regionów](luis-reference-regions.md) publikowania w tym [Europa](luis-reference-regions.md#publishing-to-europe), i [Australii](luis-reference-regions.md#publishing-to-australia). Regiony publikowania różnią się od tworzenia regionów. Upewnij się, że tworzenie aplikacji w regionie tworzenia odpowiednich do regionu publikowania, który ma.

## <a name="unassign-key"></a>Cofnij przypisanie klucza

* W **Lista zasobów i klucze**, kliknij ikonę Kosza obok jednostki, aby cofnąć to przypisanie. Następnie kliknij przycisk **OK** w komunikacie potwierdzenia, aby potwierdzić usunięcie.
 
    ![Cofnij przypisanie jednostek](./media/luis-manage-keys/unassign-key.png)

> [!NOTE]
> Cofania przypisania klucz usługi LUIS nie spowoduje usunięcia go z subskrypcji platformy Azure.

## <a name="next-steps"></a>Kolejne kroki

Użycia tego klucza, aby opublikować aplikację w **Opublikuj aplikację** strony. Aby uzyskać instrukcje dotyczące publikowania, zobacz [Opublikuj aplikację](luis-how-to-publish-app.md).