---
title: 'Usługa Azure AD Connect: konto usługi ADSync | Dokumenty firmy Microsoft'
description: W tym temacie opisano konto usługi ADSync i przedstawiono najlepsze rozwiązania dotyczące konta.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67478721"
---
# <a name="adsync-service-account"></a>Konto usługi ADSync
Usługa Azure AD Connect instaluje usługę lokalną, która organizuje synchronizację między usługą Active Directory a usługą Azure Active Directory.  Usługa synchronizacji usługi Microsoft Azure AD Sync (ADSync) działa na serwerze w środowisku lokalnym.  Poświadczenia usługi są domyślnie ustawiane w instalacjach ekspresowych, ale mogą być dostosowywane do wymagań zabezpieczeń organizacji.  Te poświadczenia nie są używane do łączenia się z lasami lokalnymi lub usługą Azure Active Directory.

Wybranie konta usługi ADSync jest ważną decyzją planowania, która należy podjąć przed zainstalowaniem usługi Azure AD Connect.  Każda próba zmiany poświadczeń po instalacji spowoduje, że usługa nie zostanie uruchomiony, utracie dostępu do bazy danych synchronizacji i nie można uwierzytelnić się za pomocą połączonych katalogów (azure i usług AD DS).  Synchronizacja nie nastąpi, dopóki nie zostaną przywrócone oryginalne poświadczenia.

## <a name="the-default-adsync-service-account"></a>Domyślne konto usługi ADSync

Po uruchomieniu na serwerze członkowskim usługa AdSync jest uruchamiana w kontekście konta usługi wirtualnej (VSA).  Ze względu na ograniczenie produktu niestandardowe konto usługi jest tworzone po zainstalowaniu na kontrolerze domeny.  Jeśli konto usługi ustawień ekspresowych nie spełnia wymagań zabezpieczeń organizacji, należy wdrożyć usługę Azure AD Connect, wybierając opcję Dostosuj.  Następnie wybierz opcję konta usługi, która spełnia wymagania organizacji.

>[!NOTE]
>Domyślne konto usługi po zainstalowaniu na kontrolerze domeny ma postać Domain\AAD_InstallationIdentifier.  Hasło dla tego konta jest generowane losowo i stanowi poważne wyzwanie dla odzyskiwania i rotacji haseł.  Firma Microsoft zaleca dostosowanie konta usługi podczas początkowej instalacji na kontrolerze domeny w celu użycia autonomicznego lub grupowego konta usługi zarządzanej (sMSA / gMSA)

|Lokalizacja usługi Azure AD Connect|Utworzono konto usługi|
|-----|-----|
|Serwer członkowski|USŁUGA NT\AdSync|
|Kontroler domeny|Domena\AAD_74dc30c01e80 (patrz uwaga)|

## <a name="custom-adsync-service-accounts"></a>Niestandardowe konta usługi ADSync
Firma Microsoft zaleca uruchomienie usługi ADSync w kontekście konta usługi wirtualnej lub autonomicznego lub grupowego konta usługi zarządzanej.  Administrator domeny może również utworzyć konto usługi aprowizowane w celu spełnienia określonych wymagań dotyczących zabezpieczeń organizacji.   Aby dostosować konto usługi używane podczas instalacji, wybierz opcję Dostosuj na stronie Ustawienia ekspresowe poniżej.   Dostępne są następujące opcje:

- konto domyślne — usługa Azure AD Connect udostępni konto usługi w sposób opisany powyżej
- zarządzane konto usługi — użyj autonomicznego lub grupowego msa aprowidzonego przez administratora
- konto domeny — użyj konta usługi domeny aprowizowanego przez administratora

![](media/concept-adsync-service-account/adsync1.png)

![](media/concept-adsync-service-account/adsync2.png)

## <a name="diagnosing-adsync-service-account-changes"></a>Diagnozowanie zmian konta usługi ADSync
Zmiana poświadczeń usługi ADSync po instalacji spowoduje, że usługa nie zostanie uruchomiony, utracie dostępu do bazy danych synchronizacji i nie można uwierzytelnić się za pomocą połączonych katalogów (azure i usług AD DS).  Przyznanie dostępu do bazy danych do nowego konta usługi ADSync jest niewystarczające do odzyskania po tym problemie. Synchronizacja nie nastąpi, dopóki nie zostaną przywrócone oryginalne poświadczenia.

Usługa ADSync wyemituje komunikat na poziomie błędu do dziennika zdarzeń, gdy nie można go uruchomić.  Zawartość wiadomości będzie się różnić w zależności od tego, czy wbudowana baza danych (localdb) lub pełny SQL jest w użyciu.  Poniżej przedstawiono przykłady wpisów dziennika zdarzeń, które mogą być obecne.

### <a name="example-1"></a>Przykład 1

Nie można odnaleźć kluczy szyfrowania usługi AdSync i zostały one odtworzone.  Synchronizacja nie nastąpi, dopóki ten problem nie zostanie rozwiązany.

Rozwiązywanie tego problemu Klucze szyfrowania usługi Microsoft Azure AD Sync staną się niedostępne w przypadku zmiany poświadczeń logowania usługi AdSync.  Jeśli poświadczenia zostały zmienione, użyj aplikacji Usługi, aby zmienić konto logowania z powrotem do pierwotnie skonfigurowaną wartość (np. NT SERVICE\AdSync) i uruchom ponownie usługę.  Spowoduje to natychmiastowe przywrócenie poprawnego działania usługi AdSync.

Więcej informacji można znaleźć w poniższym [artykule.](https://go.microsoft.com/fwlink/?linkid=2086764)

### <a name="example-2"></a>Przykład 2

Nie można uruchomić usługi, ponieważ nie można ustanowić połączenia z lokalną bazą danych (localdb).

Rozwiązywanie tego problemu Usługa Microsoft Azure AD Sync utraci uprawnienia dostępu do lokalnego dostawcy bazy danych, jeśli zostaną zmienione poświadczenia logowania usługi AdSync.  Jeśli poświadczenia zostały zmienione, użyj aplikacji Usługi, aby zmienić konto logowania z powrotem na pierwotnie skonfigurowaną wartość (np. NT SERVICE\AdSync) i uruchom ponownie usługę.  Spowoduje to natychmiastowe przywrócenie poprawnego działania usługi AdSync.

Więcej informacji można znaleźć w poniższym [artykule.](https://go.microsoft.com/fwlink/?linkid=2086764)

Dodatkowe szczegóły Dostawca zwrócił następujące informacje o błędzie:
 

``` 
OriginalError=0x80004005 OLEDB Provider error(s): 
Description  = 'Login timeout expired'
Failure Code = 0x80004005
Minor Number = 0 
Description  = 'A network-related or instance-specific error has occurred while establishing a connection to SQL Server. Server is not found or not accessible. Check if instance name is correct and if SQL Server is configured to allow remote connections. For more information see SQL Server Books Online.'
```
## <a name="next-steps"></a>Następne kroki
Dowiedz się więcej na temat [integrowania tożsamości lokalnych z usługą Azure Active Directory](whatis-hybrid-identity.md).