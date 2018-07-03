---
title: Współpracuj z innymi współautorami nad LUIS aplikacji na platformie Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak współpraca z innymi współautorami nad aplikacjami Language Understanding (LUIS).
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 03/05/2018
ms.author: v-geberr
ms.openlocfilehash: 55c50f5276e2cf97ac98d75b8fc2bd56f1c5d644
ms.sourcegitcommit: 4597964eba08b7e0584d2b275cc33a370c25e027
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2018
ms.locfileid: "37340417"
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
