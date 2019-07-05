---
title: 'Program Azure AD Connect: Konto usługi ADSync | Dokumentacja firmy Microsoft'
description: Ten temat opisuje konta usługi ADSync i przedstawiono najlepsze rozwiązania dotyczące tego konta.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 06/27/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: f228da5afc5998d8fa59ce2d720cec4c9f955b67
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/29/2019
ms.locfileid: "67478721"
---
# <a name="adsync-service-account"></a>Konto usługi ADSync
Azure AD Connect instaluje usługi w środowisku lokalnym, który organizuje synchronizacja usługi Active Directory i Azure Active Directory.  Usługa synchronizacji programu Microsoft Azure AD Sync (ADSync) działa na serwerze w danym środowisku w środowisku lokalnym.  Poświadczenia usługi są domyślnie w przypadku instalacji ekspresowej, ale można dostosować zgodnie z wymaganiami zabezpieczeń organizacji.  Te poświadczenia nie są używane do nawiązywania połączenia z lokalnymi lasami lub Azure Active Directory.

Wybieranie ADSync konto usługi jest ważnym krokiem planowania się przed zainstalowaniem programu Azure AD Connect.  Dowolne próba zmiany poświadczeń po instalacji spowoduje niepowodzenie uruchomienia, usługi utraty dostępu do bazy danych synchronizacji i przechodzenia do uwierzytelniania za pomocą połączonych katalogów (Azure AD DS).  Synchronizacja nie zostanie przeprowadzona, dopóki nie zostaną przywrócone oryginalne poświadczenia.

## <a name="the-default-adsync-service-account"></a>Domyślne konto usługi ADSync

Po uruchomieniu na serwerze członkowskim, usługa AdSync działa w kontekście z wirtualnych usługa konta (VSA).  Z powodu ograniczenia produktu niestandardowe konto usługi jest tworzone podczas instalowania na kontrolerze domeny.  Jeśli konto usługi ustawienia Express nie spełnia wymagań dotyczących zabezpieczeń organizacji, należy wdrożyć program Azure AD Connect, wybierając opcję Dostosuj.  Następnie wybierz opcję konta usługi, która spełnia wymagania organizacji.

>[!NOTE]
>Domyślne konto usługi podczas instalowania na kontrolerze domeny ma postać Domain\AAD_InstallationIdentifier.  Hasło dla tego konta jest generowany losowo i przedstawia istotnym wyzwaniom dla obrotu odzyskiwania i hasło.  Firma Microsoft zaleca dostosowanie konto usługi podczas początkowej instalacji na kontrolerze domeny, można użyć albo autonomiczny lub konta grupy usługi zarządzanej (autonomiczne zarządzane konta usług / gMSA)

|Lokalizacja platformy Azure AD Connect|Utworzono konto usługi|
|-----|-----|
|Serwer członkowski|NT SERVICE\ADSync|
|Kontroler domeny|Domain\AAD_74dc30c01e80 (patrz uwaga)|

## <a name="custom-adsync-service-accounts"></a>Niestandardowe kont usługi ADSync
Firma Microsoft zaleca uruchamianie ADSync usługi w kontekście konta usługi wirtualnej lub autonomiczny lub konta grupy usługi zarządzanej.  Administrator domeny może też utworzyć konto usługi aprowizacji zgodnie z wymaganiami zabezpieczeń organizacji.   Aby dostosować konto usługi używane podczas instalacji, wybierz opcję Dostosuj na stronie ustawień ekspresowych poniżej.   Dostępne są następujące opcje:

- domyślne konto — program Azure AD Connect przydzieli konta usługi, zgodnie z powyższym opisem
- zarządzane konto usługi — użyj autonomicznej lub grupy zarządzanych kont usług udostępnionych przez administratora
- konto domeny — użyj domeny do konta udostępnionych przez administratora usługi

![](media/concept-adsync-service-account/adsync1.png)

![](media/concept-adsync-service-account/adsync2.png)

## <a name="diagnosing-adsync-service-account-changes"></a>Diagnozowanie zmiany konta usługi ADSync
Zmiana poświadczeń usługi ADSync po instalacji spowoduje niepowodzenie uruchomienia, usługi, utraty dostępu do bazy danych synchronizacji i przechodzenia do uwierzytelniania za pomocą połączonych katalogów (Azure AD DS).  Udzielanie dostępu do bazy danych do nowego konta usługi ADSync jest za mała, aby rozwiązać ten problem. Synchronizacja nie zostanie przeprowadzona, dopóki nie zostaną przywrócone oryginalne poświadczenia.

Usługi ADSync będzie wystawiać poziomu komunikat o błędzie w dzienniku zdarzeń, gdy nie można uruchomić.  Zawartość komunikatu różnią się w zależności od tego, czy wbudowaną bazą danych (localdb) lub Pełna baza danych SQL jest w użyciu.  Poniżej przedstawiono przykładowe wpisy w dzienniku zdarzeń, które mogą być obecne.

### <a name="example-1"></a>Przykład 1

Klucze szyfrowania usługi AdSync nie można odnaleźć i zostały utworzone ponownie.  Synchronizacja nie będzie wykonywana, dopóki ten problem nie zostanie poprawiona.

Rozwiązywanie problemów z tym problem Microsoft Azure AD Sync klucze szyfrowania, stanie się niedostępna w przypadku zmiany poświadczeń dziennika dla usługi AdSync.  Jeżeli poświadczenia zostały zmienione, należy użyć aplikacji usługi, aby zmienić konto logowania do jego początkowo skonfigurowana wartość (np.) NT SERVICE\AdSync) i uruchom ponownie usługę.  Spowoduje to natychmiastowe przywrócenie prawidłowego działania usługi AdSync.

Można znaleźć w następującym [artykułu](https://go.microsoft.com/fwlink/?linkid=2086764) Aby uzyskać więcej informacji.

### <a name="example-2"></a>Przykład 2

Usługa nie może uruchomić, ponieważ nie można nawiązać połączenia z bazą danych lokalnych (localdb).

Rozwiązywanie problemów z tej usługi problem Microsoft Azure AD Sync utraci uprawnienia dostępu do lokalnej bazy danych dostawcy w przypadku zmiany poświadczeń dziennika dla usługi AdSync.  Jeżeli poświadczenia zostały zmienione za pomocą aplikacji usługi można zmienić konto logowania do jego początkowo skonfigurowana wartość (np.) NT SERVICE\AdSync) i uruchom ponownie usługę.  Spowoduje to natychmiastowe przywrócenie prawidłowego działania usługi AdSync.

Można znaleźć w następującym [artykułu](https://go.microsoft.com/fwlink/?linkid=2086764) Aby uzyskać więcej informacji.

Dodatkowe szczegóły następujące informacje o błędzie został zwrócony przez dostawcę:
 

``` 
OriginalError=0x80004005 OLEDB Provider error(s): 
Description  = 'Login timeout expired'
Failure Code = 0x80004005
Minor Number = 0 
Description  = 'A network-related or instance-specific error has occurred while establishing a connection to SQL Server. Server is not found or not accessible. Check if instance name is correct and if SQL Server is configured to allow remote connections. For more information see SQL Server Books Online.'
```
## <a name="next-steps"></a>Kolejne kroki
Dowiedz się więcej na temat [integrowania tożsamości lokalnych z usługą Azure Active Directory](whatis-hybrid-identity.md).