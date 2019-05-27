---
title: Konfigurowanie rozszerzenia serwera NPS dla usługi Azure MFA — usługi Azure Active Directory
description: Po zainstalowaniu rozszerzenia serwera NPS, na użytek te kroki konfiguracji zaawansowanej, takie jak listy dozwolonych adresów IP i wymiany nazwa UPN.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: b8ac0497b13dad6795e8dc7ffaf761fe887a9953
ms.sourcegitcommit: cfbc8db6a3e3744062a533803e664ccee19f6d63
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/21/2019
ms.locfileid: "65988624"
---
# <a name="advanced-configuration-options-for-the-nps-extension-for-multi-factor-authentication"></a>Zaawansowane opcje konfiguracji dla rozszerzenia serwera NPS do uwierzytelniania wieloskładnikowego

Rozszerzenia serwera zasad sieciowych (NPS) rozszerza funkcje oparte na chmurze usługi Azure Multi-Factor Authentication na infrastrukturę lokalną. W tym artykule założono, że już zainstalowane rozszerzenie i teraz chcesz wiedzieć, jak dostosować rozszerzenie dla Ciebie potrzeb. 

## <a name="alternate-login-id"></a>Identyfikatora logowania alternatywnej

Ponieważ rozszerzenia serwera NPS, które łączy się zarówno lokalnie, jak i chmury z katalogami, może wystąpić problem, gdzie swoje nazwy podmiotu zabezpieczeń użytkownika (UPN) w środowisku lokalnym nie są zgodne z nazwami w chmurze. Aby rozwiązać ten problem, należy użyć alternatywnych identyfikatorów logowania. 

W ramach rozszerzenia serwera NPS należy wyznaczyć atrybut usługi Active Directory do użycia dla usługi Azure Multi-Factor Authentication zamiast nazwy UPN. Dzięki temu można chronić zasoby w środowisku lokalnym za pomocą weryfikacji dwuetapowej, bez konieczności modyfikacji sieci UPN lokalnie. 

Aby skonfigurować alternatywnych identyfikatorów logowania, przejdź do `HKLM\SOFTWARE\Microsoft\AzureMfa` i edytować następujące wartości rejestru:

| Name (Nazwa) | Type | Wartość domyślna | Opis |
| ---- | ---- | ------------- | ----------- |
| LDAP_ALTERNATE_LOGINID_ATTRIBUTE | string | Pusty | Należy określić nazwę atrybut usługi Active Directory, który chcesz użyć zamiast nazwy UPN. Ten atrybut jest używany jako atrybut AlternateLoginId. Jeśli ta wartość rejestru jest równa [nieprawidłowy atrybut usługi Active Directory](https://msdn.microsoft.com/library/ms675090.aspx) (na przykład wiadomości e-mail lub nazwa wyświetlana), następnie wartość atrybutu jest używany zamiast nazwy UPN użytkownika dla uwierzytelniania. Jeśli ta wartość rejestru jest pusty lub nie skonfigurowano, następnie AlternateLoginId jest wyłączona i nazwa UPN użytkownika jest używany do uwierzytelniania. |
| LDAP_FORCE_GLOBAL_CATALOG | wartość logiczna | Fałsz | Aby wymusić użytek wyszukiwania LDAP wykazu globalnego, podczas wyszukiwania AlternateLoginId, należy użyć tej flagi. Konfigurowanie kontrolera domeny jako wykazu globalnego, Dodaj atrybut AlternateLoginId do wykazu globalnego i włączysz tę flagę. <br><br> Jeśli LDAP_LOOKUP_FORESTS skonfigurowano (Niepuste), **ta flaga jest wymuszana jako PRAWDA**, niezależnie od wartości tego ustawienia rejestru. W tym przypadku rozszerzenia serwera NPS wymaga wykazu globalnego, należy skonfigurować za pomocą atrybutu AlternateLoginId dla każdego lasu. |
| LDAP_LOOKUP_FORESTS | string | Pusty | Podaj Rozdzielana średnikami lista lasów do wyszukania. Na przykład *contoso.com;foobar.com*. Jeśli ta wartość rejestru jest skonfigurowany, rozszerzenia serwera NPS iteracyjne wyszukuje wszystkie lasy w kolejności, w jakiej zostały wymienione, a zwraca pierwszą wartość AlternateLoginId się pomyślnie. Jeśli ta wartość rejestru nie jest skonfigurowane, wyszukiwanie AlternateLoginId jest ograniczona do bieżącej domeny.|

Informacje dotyczące rozwiązywania problemów z alternatywne identyfikatory należy używać zalecane czynności [błędy Identyfikatora logowania alternatywny](howto-mfa-nps-extension-errors.md#alternate-login-id-errors).

## <a name="ip-exceptions"></a>Wyjątki adresu IP

Jeśli potrzebujesz do monitorowania dostępności serwera, np. Jeśli usługi równoważenia obciążenia, sprawdź serwerów, które są uruchomione przed wysłaniem obciążeń, które nie mają tych sprawdzeń zablokowana przez weryfikację żądania. Zamiast tego należy utworzyć listę adresów IP, które są używane przez konta usług, a następnie wyłącz wymagania dotyczące uwierzytelniania wieloskładnikowego dla tej listy.

Aby skonfigurować listy dozwolonych adresów IP, przejdź do `HKLM\SOFTWARE\Microsoft\AzureMfa` i skonfiguruj następujące wartości rejestru:

| Name (Nazwa) | Type | Wartość domyślna | Opis |
| ---- | ---- | ------------- | ----------- |
| IP_WHITELIST | string | Pusty | Podaj Rozdzielana średnikami lista adresów IP. Zawierać adresy IP maszyn, których pochodzą żądania usług, takich jak serwer NAS/sieci VPN. Zakresy adresów IP i podsieci nie są obsługiwane. <br><br> Na przykład *10.0.0.1;10.0.0.2;10.0.0.3*.

Gdy żądania pochodzą z adresu IP, który znajduje się w `IP_WHITELIST`, weryfikacji dwuetapowej jest pomijany. Na liście adresów IP jest porównywany z adresu IP, który znajduje się w *ratNASIPAddress* atrybut żądanie usługi RADIUS. Jeśli żądanie usługi RADIUS jest oferowana w bez atrybutu ratNASIPAddress, są rejestrowane następujące ostrzeżenie: "P_WHITE_LIST_WARNING::IP listy dozwolonych jest ignorowany, ponieważ źródłowy adres IP nie ma żądania usługi RADIUS w atrybucie NasIpAddress."

## <a name="next-steps"></a>Kolejne kroki

[Resolve error messages from the NPS extension for Azure Multi-Factor Authentication](howto-mfa-nps-extension-errors.md) (Rozstrzyganie komunikatów o błędach z rozszerzenia serwera NPS dotyczących usługi Azure Multi-Factor Authentication)
