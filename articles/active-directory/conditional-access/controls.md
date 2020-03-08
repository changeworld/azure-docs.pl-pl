---
title: Niestandardowe kontrolki dostępu warunkowego usługi Azure AD
description: Dowiedz się, jak kontrolki niestandardowe w Azure Active Directory dostęp warunkowy.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.date: 02/25/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3fff08690eb2807fbbd50f297761c57d3fef88fe
ms.sourcegitcommit: bc792d0525d83f00d2329bea054ac45b2495315d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/06/2020
ms.locfileid: "78671843"
---
# <a name="custom-controls-preview"></a>Formanty niestandardowe (wersja zapoznawcza)

Kontrolki niestandardowe są możliwością Azure Active Directory — wersja Premium wersji P1. W przypadku korzystania z kontrolek niestandardowych użytkownicy są przekierowywani do zgodnej usługi, aby spełnić dalsze wymagania poza Azure Active Directory. W celu spełnienia tej kontrolki przeglądarka użytkownika zostanie przekierowana do usługi zewnętrznej, wykonuje wszelkie wymagane operacje uwierzytelniania lub walidacji, a następnie zostanie przekierowana z powrotem do Azure Active Directory. Azure Active Directory weryfikuje odpowiedź i, jeśli użytkownik został pomyślnie uwierzytelniony lub zweryfikowany, użytkownik kontynuuje w przepływie dostępu warunkowego.

Te kontrolki umożliwiają użycie niektórych usług zewnętrznych lub niestandardowych jako kontroli dostępu warunkowego i ogólnie rozszerzając możliwości dostępu warunkowego.

Dostawcy oferujący obecnie zgodną usługę obejmują:

- [Duo — zabezpieczenia](https://duo.com/docs/azure-ca)
- [Entrust Datacard](https://www.entrustdatacard.com/products/authentication/intellitrust)
- [GSMA](https://mobileconnect.io/azure/)
- [Wyślij polecenie ping do tożsamości](https://documentation.pingidentity.com/pingid/pingidAdminGuide/index.shtml#pid_c_AzureADIntegration.html)
- [RSA](https://community.rsa.com/docs/DOC-81278)
- [SecureAuth](https://docs.secureauth.com/pages/viewpage.action?pageId=47238992#)
- [Silverfort](https://www.silverfort.io/company/using-silverfort-mfa-with-azure-active-directory/)
- [Adres VIP firmy Symantec](https://help.symantec.com/home/VIP_Integrate_with_Azure_AD)
- [Firmy Thales (firmy Gemalto)](https://resources.eu.safenetid.com/help/AzureMFA/Azure_Help/Index.htm)
- [Trusona](https://www.trusona.com/docs/azure-ad-integration-guide)

Aby uzyskać więcej informacji na temat tych usług, skontaktuj się bezpośrednio z dostawcami.

## <a name="creating-custom-controls"></a>Tworzenie niestandardowych kontrolek

Aby utworzyć kontrolkę niestandardową, należy najpierw skontaktować się z dostawcą, którego chcesz użyć. Każdy dostawca firmy innej niż Microsoft ma własny proces i wymagania, aby zarejestrować się, subskrybować lub w inny sposób stać się częścią usługi i wskazać, że chcesz zintegrować się z dostępem warunkowym. W tym momencie dostawca dostarczy blok danych w formacie JSON. Te dane umożliwiają dostawcy i dostęp warunkowy współdziałanie dla dzierżawy, tworzy nową kontrolkę i definiuje, jak dostęp warunkowy może stwierdzić, czy użytkownicy pomyślnie przeprowadzili weryfikację u dostawcy.

Kontrolki niestandardowe nie mogą być używane z automatyzacją usługi Identity Protection wymagającą uwierzytelniania wieloskładnikowego lub podniesienia poziomu ról w programie Privileged Identity Manager (PIM).

Skopiuj dane JSON, a następnie wklej je do powiązanego pola tekstowego. Nie wprowadzaj żadnych zmian w pliku JSON, chyba że jawnie zrozumiesz wprowadzaną zmianę. Wprowadzenie jakichkolwiek zmian może spowodować przerwanie połączenia między dostawcą i firmą Microsoft oraz zablokowanie użytkownika i użytkowników poza kontami.

Opcja tworzenia kontrolki niestandardowej znajduje się w sekcji **Zarządzanie** na stronie **dostęp warunkowy** .

![Kontrola](./media/controls/82.png)

Kliknięcie przycisku **Nowy formant niestandardowy**powoduje otwarcie bloku z polem tekstowym dla danych JSON formantu.  

![Kontrola](./media/controls/81.png)

## <a name="deleting-custom-controls"></a>Usuwanie formantów niestandardowych

Aby usunąć kontrolkę niestandardową, należy najpierw upewnić się, że nie jest ona używana w żadnym z zasad dostępu warunkowego. Po ukończeniu:

1. Przejdź do listy formantów niestandardowych
1. Kliknij przycisk...  
1. Wybierz pozycję **Usuń**.

## <a name="editing-custom-controls"></a>Edytowanie formantów niestandardowych

Aby edytować kontrolkę niestandardową, należy usunąć bieżącą kontrolkę i utworzyć nową kontrolkę z zaktualizowanymi informacjami.

## <a name="next-steps"></a>Następne kroki

- [Wspólne zasady dostępu warunkowego](concept-conditional-access-policy-common.md)

- [Tryb tylko do raportowania](concept-conditional-access-report-only.md)

- [Symulowanie zachowania logowania za pomocą narzędzia What If dostępu warunkowego](troubleshoot-conditional-access-what-if.md)
