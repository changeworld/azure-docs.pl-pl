---
title: Współpracuj z innymi — LUIS
titleSuffix: Azure Cognitive Services
description: Właściciel aplikacji można dodać współpracowników do aplikacji. Współpracownicy te można modyfikować modelu, uczenie i publikowanie aplikacji.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 07/29/2019
ms.author: diberry
ms.openlocfilehash: dbc27176e7a300c0799e326acb10b99ac663fa89
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/30/2019
ms.locfileid: "68638125"
---
# <a name="how-to-manage-authors-and-collaborators"></a>Jak zarządzać autorzy i współpracowników 

Właściciel aplikacji można dodać współpracowników do aplikacji. Współpracownicy te można modyfikować modelu, uczenie i publikowanie aplikacji. 

<a name="owner-and-collaborators"></a>

## <a name="add-collaborator"></a>Dodaj współpracowników

Aplikacji ma jednego autora, jego właściciela, ale może mieć wiele współpracowników. Aby umożliwić współpracownikom edytować aplikacją usługi LUIS, należy dodać adres e-mail, używane do dostępu do portalu usługi LUIS do listy współpracowników. Po dodaniu ich wyświetlany przez aplikację w portalu usługi LUIS.

1. Wybierz **Zarządzaj** w prawym górnym menu wybierz **współpracowników** w menu po lewej stronie.

2. Wybierz **Dodaj współpracownika** na pasku narzędzi.

    [![Dodaj współpracowników](./media/luis-how-to-collaborate/add-collaborator.png "Dodaj współpracownika")](./media/luis-how-to-collaborate/add-collaborator.png#lightbox)

3. Wprowadź adres e-mail, którego używa współpracownika, aby zalogować się do portalu usługi LUIS.

    ![Dodaj adres e-mail współpracownika firmy](./media/luis-how-to-collaborate/add-collaborator-pop-up.png)

## <a name="transfer-of-ownership"></a>Przeniesienie prawa własności

Gdy usługa LUIS nie obsługuje obecnie przeniesienie prawa własności, można wyeksportować aplikacji, a inny użytkownik usługi LUIS można zaimportować aplikację. Może to być niewielkie różnice w wyniki usługi LUIS między dwiema aplikacjami. 

## <a name="azure-active-directory-resources"></a>Zasoby platformy Azure Active Directory

Jeśli używasz usługi [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/) (Azure AD) w organizacji, language UNDERSTANDING (Luis) potrzebuje uprawnień do informacji o dostępie użytkowników, gdy chcą korzystać z Luis. Zasoby, które wymaga usługi LUIS są minimalne. 

Szczegółowy opis zostanie wyświetlony, gdy spróbujesz zarejestrować się przy użyciu konta, które ma zgody administratora, lub nie wymagają zgody administratora, takich jak zgody administratora:

* Umożliwia logowanie do aplikacji za pomocą konta organizacyjnego i umożliwia aplikacji odczytanie profilu. Umożliwia również aplikacji na odczytywanie podstawowych informacji o firmie. Daje to LUIS uprawnienia do odczytu podstawowych danych profilowych, takich jak identyfikator użytkownika, adres e-mail, nazwa
* Zezwala aplikacji na wyświetlanie i aktualizowanie danych, nawet wtedy, gdy nie używasz obecnie aplikacji. Uprawnienie jest wymagane do odświeżenia tokenu dostępu użytkownika.


## <a name="azure-active-directory-tenant-user"></a>Użytkownik dzierżawy usługi Azure Active Directory

Usługa LUIS używa standardowych przepływu wyrażania zgody usługi Azure Active Directory (Azure AD). 

Administrator dzierżawy powinny współpracować bezpośrednio z użytkownikiem, który musi mieć dostęp udzielony do korzystania z usługi LUIS w usłudze Azure AD. 

* Po pierwsze użytkownik zaloguje się do usługi LUIS i widzi wyskakującego okna dialogowego, wymagające zatwierdzenia administratora. Użytkownik skontaktuje się z administratorem dzierżawy przed kontynuowaniem. 
* Po drugie Administrator dzierżawy zaloguje się do usługi LUIS i widzi zgody przepływu wyskakującego okna dialogowego. To okno dialogowe, administrator musi udzielić uprawnień dla użytkownika. Gdy administrator zaakceptuje uprawnień, użytkownik będzie mógł kontynuować z użyciem usługi LUIS. Jeśli administrator dzierżawy nie zaloguje się do usługi LUIS, administrator będzie mógł [](https://account.activedirectory.windowsazure.com/r#/applications) uzyskać zgodę na Luis, jak pokazano na poniższym zrzucie ekranu. Zwróć uwagę na to, że lista jest filtrowana do elementów `LUIS`, które zawierają nazwę.

![Uprawnienia usługi Azure active directory, witryna sieci Web aplikacji](./media/luis-how-to-collaborate/tenant-permissions.png)

Jeśli administrator dzierżawy chce, aby niektórzy użytkownicy korzystali z LUIS, istnieje kilka możliwych rozwiązań:
* Podawanie "zgody administratora" (wyrażanie zgody wszystkim użytkownikom usługi Azure AD), a następnie ustawienie opcji "tak" jako "wymagane przypisanie użytkownika" we właściwościach aplikacji dla przedsiębiorstw i przypisanie/dodanie tylko odpowiednich użytkowników do aplikacji. W przypadku tej metody administrator nadal zapewnia dostęp do aplikacji "Zgoda na administrowanie", jednak można kontrolować użytkowników, którzy mają do nich dostępu.
* Drugie rozwiązanie, korzysta z [usługi Azure AD interfejs API programu Graph](https://docs.microsoft.com/graph/azuread-identity-access-management-concept-overview) , aby zapewnić zgodę na poszczególnych użytkowników. 

Dowiedz się więcej na temat użytkowników i zgody usługi Azure Active Directory: 
* [Ograniczanie aplikacji](../../active-directory/develop/howto-restrict-your-app-to-a-set-of-users.md) do zestawu użytkowników

### <a name="user-accounts-with-multiple-emails-for-collaborators"></a>Konta użytkowników z wielu wiadomości e-mail pod kątem współpracowników

Jeśli dodasz współpracowników z aplikacją usługi LUIS, określasz adres dokładny adres e-mail współpracownika wymaga korzystania z usługi LUIS do niej jako współpracownika. Gdy usługi Azure Active Directory (Azure AD) umożliwia pojedynczego użytkownika mieć więcej niż jedno konto e-mail używane zamiennie, LUIS wymaga od użytkownika o zalogowanie się na adres e-mail określony na liście współpracownika.

