---
title: Zarządzanie ustawieniami konta w LUIS | Dokumentacja firmy Microsoft
description: Umożliwia zarządzanie ustawieniami konta LUIS witryny sieci Web.
titleSuffix: Azure
services: cognitive-services
author: v-geberr
manager: Kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 06/04/2018
ms.author: v-geberr
ms.openlocfilehash: 1f22112a38bf32af03ffaf0493db16839b3fe794
ms.sourcegitcommit: 6eb14a2c7ffb1afa4d502f5162f7283d4aceb9e2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/25/2018
ms.locfileid: "36749967"
---
# <a name="manage-your-luis-account"></a>Zarządzanie kontem LUIS
Dwa kluczowych informacji dotyczących konta LUIS są konta użytkowników i tworzenia klucza. Informacje logowania jest zarządzana w [account.microsoft.com](https://account.microsoft.com). Klucz tworzenia odbywa się z [LUIS][LUIS] witryny sieci Web **ustawienia** strony. 

## <a name="authoring-key"></a>Tworzenie klucza

Ten pojedynczy, określonego regionu tworzenia klucza na **ustawienia** strony, umożliwia tworzenie wszystkie aplikacje z [LUIS][LUIS] witryny sieci Web, jak również [ Tworzenie interfejsów API](https://aka.ms/luis-authoring-api). Dla wygody tworzenia klucza może wprowadzać [ograniczone](luis-boundaries.md) liczba punktu końcowego wysyła zapytanie do każdego miesiąca. 

![Strona Ustawienia LUIS](./media/luis-how-to-account-settings/account-settings.png)

Tworzenia klucz jest używany dla wszystkich aplikacji, użytkownika, a także wszystkie aplikacje, które są wyświetlane jako współpracownika.

## <a name="authoring-key-regions"></a>Tworzenie klucza regionów
Dotyczy tworzenia klucza [tworzenia region](luis-reference-regions.md#publishing-regions). Klucz nie działa w innym regionie. 

## <a name="reset-authoring-key"></a>Resetowanie tworzenia klucza
W przypadku złamania zabezpieczeń tworzenia klucza, należy zresetować klucz. Klucz jest resetowany na wszystkie aplikacje w [LUIS] witryny sieci Web. Jeśli tworzenie aplikacji za pomocą tworzenia interfejsów API, należy zmienić wartość `Ocp-Apim-Subscription-Key` do nowego klucza. 

## <a name="delete-account"></a>Usuń konto
Zobacz [magazynu danych i usunięcie](luis-concept-data-storage.md#accounts) informacji o jakie dane są usuwane podczas usuwania konta. 

## <a name="azure-active-directory-tenant-user"></a>Użytkownika dzierżawy usługi Azure Active Directory
LUIS używa standardowych przepływu zgody usługi Azure Active Directory (Azure AD). 

Administrator dzierżawy powinien współpracować bezpośrednio z użytkownikiem, który potrzebuje udzielono dostępu do użycia LUIS w usłudze Azure AD. 

Po pierwsze użytkownik zaloguje się do LUIS i widzi wyskakującym oknie dialogowym wymagające zatwierdzenia administratora. Użytkownik Administrator dzierżawy kontaktuje się przed kontynuowaniem. 

Po drugie Administrator dzierżawy zaloguje się do LUIS i widzi okno podręczne przepływu zgody. To okno dialogowe, administrator musi nadać uprawnienia dla użytkownika. Gdy administrator zaakceptuje uprawnienia, użytkownik będzie mógł kontynuować LUIS.

Jeśli Administrator dzierżawy nie będzie zalogować do LUIS, administrator może uzyskać dostęp do [zgody](https://account.activedirectory.windowsazure.com/r#/applications) dla LUIS. 

![Uprawnienia usługi Azure active directory przez aplikację witryny sieci Web](./media/luis-how-to-account-settings/tenant-permissions.png)

Jeśli Administrator dzierżawy chce tylko niektórych użytkowników, aby użyć LUIS, odwołują się do tego [blog tożsamość](https://blogs.technet.microsoft.com/tfg/2017/10/15/english-tips-to-manage-azure-ad-users-consent-to-applications-using-azure-ad-graph-api/).

### <a name="user-accounts-with-multiple-emails-for-collaborators"></a>Konta użytkowników z wielu wiadomości e-mail do współpracowników
Jeśli dodasz współpracowników do aplikacji LUIS są określenie adresu e-mail dokładne współpracownika musi używać LUIS jako współpracownika. Chociaż usługi Azure Active Directory (Azure AD) pozwala na jednego użytkownika, ma więcej niż jedno konto e-mail używane zamiennie, LUIS wymaga logowania się za pomocą adresu e-mail znajduje się na liście współpracownika. 


## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej o sieci [tworzenia klucza](luis-concept-keys.md#authoring-key). 

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions
