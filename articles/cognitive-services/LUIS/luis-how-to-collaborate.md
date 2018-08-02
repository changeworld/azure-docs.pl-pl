---
title: Współpracuj z innymi współautorami nad LUIS aplikacji na platformie Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak współpraca z innymi współautorami nad aplikacjami Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 07/31/2018
ms.author: diberry
ms.openlocfilehash: 99f37cb6dc5e05fc5eb4bde09685435ee57fecc6
ms.sourcegitcommit: d4c076beea3a8d9e09c9d2f4a63428dc72dd9806
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/01/2018
ms.locfileid: "39397792"
---
# <a name="how-to-manage-authors-and-collaborators"></a>Jak zarządzać autorzy i współpracowników 

Możesz współpracować z innymi osobami nad aplikacją usługi LUIS ze sobą. 

## <a name="owner-and-collaborators"></a>Właściciela i współpracowników

Aplikacji ma jednego autora, jego właściciela, ale może mieć wiele współpracowników. 

## <a name="add-collaborator"></a>Dodaj współpracowników

Aby umożliwić współpracownikom edytować aplikacją usługi LUIS w **ustawienia** strony z aplikacją usługi LUIS, wprowadź adres e-mail współpracownika, a następnie kliknij przycisk **współpracownika Dodaj**. Współpracownicy mogą Zaloguj się i edytować aplikacją usługi LUIS w tym samym czasie, w którym pracujesz w aplikacji.

![Dodaj współpracowników](./media/luis-how-to-collaborate/add-collaborator.png)

## <a name="transfer-of-ownership"></a>Przeniesienie prawa własności

Gdy usługa LUIS nie obsługuje obecnie przeniesienie prawa własności, można wyeksportować aplikacji, a inny użytkownik usługi LUIS można zaimportować aplikację. Może to być niewielkie różnice w wyniki usługi LUIS między dwiema aplikacjami. 

## <a name="azure-active-directory-tenant-user"></a>Użytkownik dzierżawy usługi Azure Active Directory

Usługa LUIS używa standardowych przepływu wyrażania zgody usługi Azure Active Directory (Azure AD). 

Administrator dzierżawy powinny współpracować bezpośrednio z użytkownikiem, który musi mieć dostęp udzielony do korzystania z usługi LUIS w usłudze Azure AD. 

Po pierwsze użytkownik zaloguje się do usługi LUIS i widzi wyskakującego okna dialogowego, wymagające zatwierdzenia administratora. Użytkownik skontaktuje się z administratorem dzierżawy przed kontynuowaniem. 

Po drugie Administrator dzierżawy zaloguje się do usługi LUIS i widzi zgody przepływu wyskakującego okna dialogowego. To okno dialogowe, administrator musi udzielić uprawnień dla użytkownika. Gdy administrator zaakceptuje uprawnień, użytkownik będzie mógł kontynuować z użyciem usługi LUIS.

Jeśli Administrator dzierżawy nie będą logować się LUIS, administrator może uzyskać dostęp do [zgody](https://account.activedirectory.windowsazure.com/r#/applications) dla usługi LUIS. 

![Uprawnienia usługi Azure active directory, witryna sieci Web aplikacji](./media/luis-how-to-account-settings/tenant-permissions.png)

Jeśli Administrator dzierżawy chce tylko niektórych użytkowników do korzystania z usługi LUIS, zapoznaj się z tym [blog tożsamość](https://blogs.technet.microsoft.com/tfg/2017/10/15/english-tips-to-manage-azure-ad-users-consent-to-applications-using-azure-ad-graph-api/).

### <a name="user-accounts-with-multiple-emails-for-collaborators"></a>Konta użytkowników z wielu wiadomości e-mail pod kątem współpracowników

Jeśli dodasz współpracowników z aplikacją usługi LUIS, określasz adres dokładny adres e-mail współpracownika wymaga korzystania z usługi LUIS do niej jako współpracownika. Gdy usługi Azure Active Directory (Azure AD) umożliwia pojedynczego użytkownika mieć więcej niż jedno konto e-mail używane zamiennie, LUIS wymaga od użytkownika o zalogowanie się na adres e-mail określony na liście współpracownika.