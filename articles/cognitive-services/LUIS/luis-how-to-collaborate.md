---
title: Współpracuj z innymi współautorami nad LUIS aplikacji na platformie Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak współpraca z innymi współautorami nad aplikacjami Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 03/05/2018
ms.author: diberry
ms.openlocfilehash: 9ea0269439b3d00bf36186cf2fd5c73311526bec
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/24/2018
ms.locfileid: "39225605"
---
# <a name="collaborate-with-others-on-language-understanding-luis-apps"></a>Współpracowanie z innymi osobami w aplikacjach Language Understanding (LUIS)  

Możesz współpracować z innymi osobami nad aplikacją usługi LUIS ze sobą. 

## <a name="owner-and-collaborators"></a>Właściciela i współpracowników
Aplikacja ma jednego właściciela, ale może mieć wiele współpracowników. 

## <a name="add-collaborator"></a>Dodaj współpracowników

Aby umożliwić współpracownikom edytować aplikacją usługi LUIS w **ustawienia** strony z aplikacją usługi LUIS, wprowadź adres e-mail współpracownika, a następnie kliknij przycisk **współpracownika Dodaj**.

![Dodaj współpracowników](./media/luis-how-to-collaborate/add-collaborator.png)

* Współpracownicy mogą Zaloguj się i edytować aplikacją usługi LUIS w tym samym czasie, w którym pracujesz w aplikacji. <!--If a collaborator edits the LUIS app, you see a notification at the top of the browser.-->
* Współpracownicy, nie można dodać inne współpracowników.

Zobacz [użytkownik dzierżawy usługi Azure Active Directory](luis-how-to-account-settings.md#azure-active-directory-tenant-user) Aby dowiedzieć się więcej na temat kont użytkowników usługi Active Directory. 

## <a name="set-application-as-public"></a>Zestaw aplikacji jako publiczny
Zobacz [dostęp do punktu końcowego aplikacji publicznych](luis-concept-security.md#public-app-endpoint-access) Aby uzyskać więcej informacji.

### <a name="transfer-of-ownership"></a>Przeniesienie prawa własności
Gdy usługa LUIS nie obsługuje obecnie przeniesienie prawa własności, można wyeksportować aplikacji, a inny użytkownik usługi LUIS można zaimportować aplikację. Może to być niewielkie różnice w wyniki usługi LUIS między dwiema aplikacjami. 
