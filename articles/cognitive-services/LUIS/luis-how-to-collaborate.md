---
title: Współpracuj z innymi
titleSuffix: Language Understanding - Azure Cognitive Services
description: Właściciel aplikacji można dodać współpracowników do aplikacji. Współpracownicy te można modyfikować modelu, uczenie i publikowanie aplikacji.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 01/23/2019
ms.author: diberry
ms.openlocfilehash: 4ccf703ffde0d4a5a0961135e81259519b0a0e41
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/07/2019
ms.locfileid: "55878973"
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

Jeśli Administrator dzierżawy chce tylko niektórych użytkowników do korzystania z usługi LUIS, istnieje kilka możliwych rozwiązań:
* Zapewniając "zgoda administratora" (zgodę dla wszystkich użytkowników usługi Azure AD), ale następnie ustawić opcję "Tak", "wymagane przypisanie użytkownika" we właściwościach aplikacji przedsiębiorstwa i na koniec Przypisz/dodać odpowiednich użytkowników do aplikacji. Przy użyciu tej metody Administrator jest zachowaniu "zgoda administratora" do aplikacji, jednak jest możliwe kontrolowanie użytkowników, którzy mogą uzyskać do niego dostęp.
* Drugim rozwiązaniem jest użycie [interfejsu API usługi Azure AD Graph](https://docs.microsoft.com/graph/azuread-identity-access-management-concept-overview) dostarczyć zgodę każdego określonego użytkownika. 

Dowiedz się więcej na temat usługi Azure active directory użytkownicy i zgody: 
* [Ograniczenia aplikacji](../../active-directory/develop/howto-restrict-your-app-to-a-set-of-users.md) grupy użytkowników

### <a name="user-accounts-with-multiple-emails-for-collaborators"></a>Konta użytkowników z wielu wiadomości e-mail pod kątem współpracowników

Jeśli dodasz współpracowników z aplikacją usługi LUIS, określasz adres dokładny adres e-mail współpracownika wymaga korzystania z usługi LUIS do niej jako współpracownika. Gdy usługi Azure Active Directory (Azure AD) umożliwia pojedynczego użytkownika mieć więcej niż jedno konto e-mail używane zamiennie, LUIS wymaga od użytkownika o zalogowanie się na adres e-mail określony na liście współpracownika.

