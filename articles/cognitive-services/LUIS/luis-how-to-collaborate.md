---
title: Współpracuj z innymi współautorami nad aplikacji usługi LUIS
titleSuffix: Azure Cognitive Services
description: Właściciel aplikacji można dodać współpracowników do aplikacji. Współpracownicy te można modyfikować modelu, uczenie i publikowanie aplikacji.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 09/10/2018
ms.author: diberry
ms.openlocfilehash: b7d108bfc0c4283e7856b93daba3f4f92af4cc5b
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "47042197"
---
# <a name="how-to-manage-authors-and-collaborators"></a>Jak zarządzać autorzy i współpracowników 

Właściciel aplikacji można dodać współpracowników do aplikacji. Współpracownicy te można modyfikować modelu, uczenie i publikowanie aplikacji. 

<a name="owner-and-collaborators"></a>

## <a name="add-collaborator"></a>Dodaj współpracowników

Aplikacji ma jednego autora, jego właściciela, ale może mieć wiele współpracowników. Aby umożliwić współpracownikom edytować aplikacją usługi LUIS, należy dodać adres e-mail, używane do dostępu do portalu usługi LUIS do listy współpracowników. Po dodaniu ich wyświetlany przez aplikację w portalu usługi LUIS.

1. Wybierz **Zarządzaj** w prawym górnym menu wybierz **współpracowników** w menu po lewej stronie.

2. Wybierz **Dodaj współpracownika** na pasku narzędzi.

    [![](./media/luis-how-to-collaborate/add-collaborator.png "Dodaj współpracowników")](./media/luis-how-to-collaborate/add-collaborator.png#lightbox)

3. Wprowadź adres e-mail, którego używa współpracownika, aby zalogować się do portalu usługi LUIS.

    ![Dodaj adres e-mail współpracownika firmy](./media/luis-how-to-collaborate/add-collaborator-pop-up.png)

## <a name="transfer-of-ownership"></a>Przeniesienie prawa własności

Gdy usługa LUIS nie obsługuje obecnie przeniesienie prawa własności, można wyeksportować aplikacji, a inny użytkownik usługi LUIS można zaimportować aplikację. Może to być niewielkie różnice w wyniki usługi LUIS między dwiema aplikacjami. 

## <a name="azure-active-directory-resources"></a>Zasoby platformy Azure Active Directory

Jeśli używasz usługi Azure Active Directory (Azure AD) w Twojej organizacji, LUIS wymaga zgody na dostęp do informacji o użytkownikach, podczas korzystania z usługi LUIS. Zasoby, które wymaga usługi LUIS są minimalne. 

Szczegółowy opis zostanie wyświetlony, gdy spróbujesz zarejestrować się przy użyciu konta, które ma zgody administratora, lub nie wymagają zgody administratora, takich jak zgody administratora:

* Umożliwia logowanie do aplikacji za pomocą konta organizacyjnego i umożliwia aplikacji odczytanie profilu. Umożliwia również aplikacji na odczytywanie podstawowych informacji o firmie.
* Zezwala aplikacji na wyświetlanie i aktualizowanie danych, nawet wtedy, gdy nie używasz obecnie aplikacji.

Pierwszy uprawnienie daje LUIS uprawnienia do odczytu danych profilu podstawowego, takich jak identyfikator użytkownika, adres e-mail, nazwy. Drugi uprawnienie jest wymagane do odświeżenia tokenu dostępu użytkownika.

## <a name="azure-active-directory-tenant-user"></a>Użytkownik dzierżawy usługi Azure Active Directory

Usługa LUIS używa standardowych przepływu wyrażania zgody usługi Azure Active Directory (Azure AD). 

Administrator dzierżawy powinny współpracować bezpośrednio z użytkownikiem, który musi mieć dostęp udzielony do korzystania z usługi LUIS w usłudze Azure AD. 

Po pierwsze użytkownik zaloguje się do usługi LUIS i widzi wyskakującego okna dialogowego, wymagające zatwierdzenia administratora. Użytkownik skontaktuje się z administratorem dzierżawy przed kontynuowaniem. 

Po drugie Administrator dzierżawy zaloguje się do usługi LUIS i widzi zgody przepływu wyskakującego okna dialogowego. To okno dialogowe, administrator musi udzielić uprawnień dla użytkownika. Gdy administrator zaakceptuje uprawnień, użytkownik będzie mógł kontynuować z użyciem usługi LUIS.

Jeśli Administrator dzierżawy nie będą logować się LUIS, administrator może uzyskać dostęp do [zgody](https://account.activedirectory.windowsazure.com/r#/applications) dla usługi LUIS. 

![Uprawnienia usługi Azure active directory, witryna sieci Web aplikacji](./media/luis-how-to-collaborate/tenant-permissions.png)

Jeśli Administrator dzierżawy chce tylko niektórych użytkowników do korzystania z usługi LUIS, zapoznaj się z tym [blog tożsamość](https://blogs.technet.microsoft.com/tfg/2017/10/15/english-tips-to-manage-azure-ad-users-consent-to-applications-using-azure-ad-graph-api/).

### <a name="user-accounts-with-multiple-emails-for-collaborators"></a>Konta użytkowników z wielu wiadomości e-mail pod kątem współpracowników

Jeśli dodasz współpracowników z aplikacją usługi LUIS, określasz adres dokładny adres e-mail współpracownika wymaga korzystania z usługi LUIS do niej jako współpracownika. Gdy usługi Azure Active Directory (Azure AD) umożliwia pojedynczego użytkownika mieć więcej niż jedno konto e-mail używane zamiennie, LUIS wymaga od użytkownika o zalogowanie się na adres e-mail określony na liście współpracownika.

