---
title: Konfigurowanie rozszerzenia serwera NPS usługi Azure MFA — usługa Azure Active Directory
description: Po zainstalowaniu rozszerzenia NPS wykonaj następujące kroki w przypadku zaawansowanej konfiguracji, takiej jak umieszczanie na białej liście adresów IP i zastępowanie siecią UPN.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9ea5b4f52fc161cb8359ef56e76e0607459d6280
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74848361"
---
# <a name="advanced-configuration-options-for-the-nps-extension-for-multi-factor-authentication"></a>Zaawansowane opcje konfiguracji rozszerzenia serwera NPS dla uwierzytelniania wieloskładnikowego

Rozszerzenie serwera zasad sieciowych (NPS) rozszerza funkcje uwierzytelniania wieloskładnikowego platformy Azure w chmurze na infrastrukturę lokalną. W tym artykule założono, że masz już zainstalowane rozszerzenie, a teraz chcesz wiedzieć, jak dostosować rozszerzenie do potrzeb. 

## <a name="alternate-login-id"></a>Alternatywny identyfikator logowania

Ponieważ rozszerzenie NPS łączy się zarówno z katalogami lokalnymi, jak i w chmurze, może wystąpić problem polegający na tym, że lokalne nazwy główne użytkowników (UPN) nie są zgodne z nazwami w chmurze. Aby rozwiązać ten problem, użyj alternatywnych identyfikatorów logowania. 

W ramach rozszerzenia NPS można wyznaczyć atrybut usługi Active Directory, który ma być używany zamiast nazwy UPN dla uwierzytelniania wieloskładnikowego platformy Azure. Dzięki temu można chronić zasoby lokalne za pomocą weryfikacji dwuetapowej bez modyfikowania lokalnych sieci UPN. 

Aby skonfigurować alternatywne identyfikatory `HKLM\SOFTWARE\Microsoft\AzureMfa` logowania, przejdź do następujących wartości rejestru i edytuj:

| Nazwa | Typ | Wartość domyślna | Opis |
| ---- | ---- | ------------- | ----------- |
| LDAP_ALTERNATE_LOGINID_ATTRIBUTE | ciąg | Pusty | Wyznacz nazwę atrybutu usługi Active Directory, którego chcesz użyć, zamiast nazwy UPN. Ten atrybut jest używany jako atrybut AlternateLoginId. Jeśli ta wartość rejestru jest ustawiona na [prawidłowy atrybut usługi Active Directory](https://msdn.microsoft.com/library/ms675090.aspx) (na przykład mail lub displayName), wartość atrybutu jest używana zamiast nazwy UPN użytkownika do uwierzytelniania. Jeśli ta wartość rejestru jest pusta lub nie skonfigurowana, funkcja AlternateLoginId jest wyłączona, a nazwa UPN użytkownika jest używana do uwierzytelniania. |
| LDAP_FORCE_GLOBAL_CATALOG | wartość logiczna | False | Ta flaga służy do wymuszania użycia wykazu globalnego do wyszukiwania LDAP podczas wyszukiwania AlternateLoginId. Skonfiguruj kontroler domeny jako wykaz globalny, dodaj atrybut AlternateLoginId do wykazu globalnego, a następnie włącz tę flagę. <br><br> Jeśli LDAP_LOOKUP_FORESTS jest skonfigurowany (nie pusty), **ta flaga jest wymuszana jako true**, niezależnie od wartości ustawienia rejestru. W takim przypadku rozszerzenie NPS wymaga wykazu globalnego, który ma być skonfigurowany z atrybutem AlternateLoginId dla każdego lasu. |
| LDAP_LOOKUP_FORESTS | ciąg | Pusty | Podaj oddzieloną od średnika listę lasów do przeszukania. Na przykład *contoso.com;foobar.com*. Jeśli ta wartość rejestru jest skonfigurowana, rozszerzenie NPS iteracyjne przeszukuje wszystkie lasy w kolejności, w jakiej zostały wymienione, i zwraca pierwszą pomyślną wartość AlternateLoginId. Jeśli ta wartość rejestru nie jest skonfigurowana, wyszukiwanie AlternateLoginId jest ograniczone do bieżącej domeny.|

Aby rozwiązać problemy z alternatywnymi identyfikatorami logowania, należy wykonać zalecane kroki dotyczące [błędów alternatywnego identyfikatora logowania](howto-mfa-nps-extension-errors.md#alternate-login-id-errors).

## <a name="ip-exceptions"></a>Wyjątki ip

Jeśli musisz monitorować dostępność serwera, na przykład jeśli moduły równoważenia obciążenia weryfikują, które serwery są uruchomione przed wysłaniem obciążeń, nie chcesz, aby te kontrole były blokowane przez żądania weryfikacji. Zamiast tego utwórz listę adresów IP, o których wiesz, że są używane przez konta usług, i wyłącz wymagania uwierzytelniania wieloskładnikowego dla tej listy.

Aby skonfigurować listę dozwolonych adresów IP, przejdź do `HKLM\SOFTWARE\Microsoft\AzureMfa` następującej wartości rejestru i skonfiguruj:

| Nazwa | Typ | Wartość domyślna | Opis |
| ---- | ---- | ------------- | ----------- |
| IP_WHITELIST | ciąg | Pusty | Podaj oddzieloną listę adresów IP o średniku. Uwzględnij adresy IP komputerów, na których pochodzą żądania usług, takie jak serwer NAS/VPN. Zakresy ip i podsieci nie są obsługiwane. <br><br> Na przykład *10,0,0,1;10,0,0,2;10,0,0,3*.

> [!NOTE]
> Ten klucz rejestru nie jest tworzony domyślnie przez instalatora i błąd pojawia się w dzienniku AuthZOptCh po ponownym uruchomieniu usługi. Ten błąd w dzienniku może być ignorowany, ale jeśli ten klucz rejestru jest tworzony i pozostawiony pusty, jeśli nie jest potrzebny, komunikat o błędzie nie zwraca.

Gdy żądanie pochodzi z adresu IP, który `IP_WHITELIST`istnieje w , weryfikacji dwuetapowej jest pomijany. Lista adresów IP jest porównywana z adresem IP podanym w atrybucie *ratNASIPAddress* żądania RADIUS. Jeśli żądanie RADIUS jest dostarczane bez atrybutu ratNASIPAddress, rejestrowane jest następujące ostrzeżenie: "P_WHITE_LIST_WARNING::IP Whitelist jest ignorowana, ponieważ w żądaniu RADIUS brakuje źródłowego adresu IP w atrybucie NasIpAddress."

## <a name="next-steps"></a>Następne kroki

[Resolve error messages from the NPS extension for Azure Multi-Factor Authentication](howto-mfa-nps-extension-errors.md) (Rozstrzyganie komunikatów o błędach z rozszerzenia serwera NPS dotyczących usługi Azure Multi-Factor Authentication)
