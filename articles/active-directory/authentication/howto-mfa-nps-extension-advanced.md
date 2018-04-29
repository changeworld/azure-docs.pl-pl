---
title: Rozszerzenie usługi Azure MFA serwera zasad Sieciowych skonfigurować | Dokumentacja firmy Microsoft
description: Po zainstalowaniu rozszerzenia serwera NPS, wykonaj następujące kroki dla konfiguracji zaawansowanej, takich jak listę dozwolonych podobnej IP i nazwy UPN zastąpienia.
services: multi-factor-authentication
documentationcenter: ''
author: MicrosoftGuyJFlo
manager: mtillman
ms.assetid: ''
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/14/2017
ms.author: joflore
ms.reviewer: richagi
ms.custom: it-pro
ms.openlocfilehash: d028be90f3a8c06b319faa442f77ab3e5c24dcb7
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2018
---
# <a name="advanced-configuration-options-for-the-nps-extension-for-multi-factor-authentication"></a>Zaawansowane opcje konfiguracji dla rozszerzenia serwera NPS uwierzytelnianie wieloskładnikowe

Rozszerzenia serwera zasad sieciowych (NPS) obejmuje funkcje usługi Azure Multi-Factor Authentication oparte na chmurze infrastruktury lokalnej. W tym artykule przyjęto założenie, już rozszerzenie zainstalowane, a teraz chcesz wiedzieć, jak dostosować rozszerzenia dla potrzeb można. 

## <a name="alternate-login-id"></a>Alternatywnego Identyfikatora logowania

Ponieważ rozszerzenia serwera NPS łączy się z lokalnymi i w chmurze katalogów, mogą wystąpić problem, gdy nie są zgodne nazwy w chmurze z lokalnych głównych nazw użytkowników (UPN). Aby rozwiązać ten problem, należy użyć alternatywnych identyfikatorów logowania. 

W ramach rozszerzenia serwera zasad Sieciowych można określić atrybut usługi Active Directory do użycia zamiast nazwy UPN dla usługi Azure Multi-Factor Authentication. Dzięki temu można chronić zasobami lokalnymi w trakcie weryfikacji dwuetapowej bez modyfikowania nazwy UPN użytkownika lokalnego. 

Aby skonfigurować alternatywnych identyfikatorów logowania, przejdź do `HKLM\SOFTWARE\Microsoft\AzureMfa` i edytować następujące wartości rejestru:

| Name (Nazwa) | Typ | Wartość domyślna | Opis |
| ---- | ---- | ------------- | ----------- |
| LDAP_ALTERNATE_LOGINID_ATTRIBUTE | ciąg | Puste | Należy określić nazwę atrybut usługi Active Directory, który ma być używany zamiast nazwy UPN. Ten atrybut jest używany jako atrybut AlternateLoginId. Jeśli ustawiono tę wartość rejestru [nieprawidłowy atrybut usługi Active Directory](https://msdn.microsoft.com/library/ms675090.aspx) (na przykład poczty lub nazwa wyświetlana), następnie wartość atrybutu jest używany zamiast nazwy UPN użytkownika do uwierzytelniania. Jeśli ta wartość rejestru jest pusty lub nie skonfigurowane, następnie AlternateLoginId jest wyłączone i nazwy UPN użytkownika jest używany do uwierzytelniania. |
| LDAP_FORCE_GLOBAL_CATALOG | wartość logiczna | False | Użyj tej flagi, aby wymusić użycie wykazu globalnego dla wyszukiwań LDAP podczas wyszukiwania AlternateLoginId. Konfigurowanie kontrolera domeny jako wykazu globalnego, Dodaj atrybut AlternateLoginId do wykazu globalnego, a następnie Włącz tę flagę. <br><br> Skonfigurowanie (nie jest pusty), LDAP_LOOKUP_FORESTS **ta flaga jest wymuszana jako true**, niezależnie od wartości ustawienia rejestru. W takim przypadku rozszerzenie serwera NPS wymaga wykazu globalnego, aby można skonfigurować za pomocą atrybutu AlternateLoginId dla każdego lasu. |
| LDAP_LOOKUP_FORESTS | ciąg | Puste | Podaj Rozdzielana średnikami lista lasów do wyszukiwania. Na przykład *contoso.com;foobar.com*. Jeśli ta wartość rejestru jest skonfigurowany, rozszerzenia serwera NPS wielokrotnie powtarzane wyszukuje wszystkich lasów w kolejności, w jakiej zostały wymienione i zwraca pierwszą wartość AlternateLoginId powiodło się. Jeśli ta wartość rejestru nie jest skonfigurowane, wyszukiwanie AlternateLoginId jest ograniczona do bieżącej domeny.|

Aby rozwiązać problemy z logowaniem alternatywnych identyfikatorów, wykonaj kroki zalecane dla [alternatywny błędy identyfikator logowania](howto-mfa-nps-extension-errors.md#alternate-login-id-errors).

## <a name="ip-exceptions"></a>Wyjątki IP

Jeśli potrzebujesz do monitorowania dostępności serwera, na przykład jeśli moduły równoważenia obciążenia Sprawdź serwerów, które są uruchomione przed wysłaniem obciążeń, nie mają być blokowane przez weryfikację żądania kontroli. Zamiast tego Utwórz listę adresów IP, które są używane przez konta usług i wyłączyć wymagania uwierzytelniania wieloskładnikowego dla tej listy. 

Aby skonfigurować listę dozwolonych adresów IP, przejdź do `HKLM\SOFTWARE\Microsoft\AzureMfa` i skonfiguruj następujące wartości rejestru: 

| Name (Nazwa) | Typ | Wartość domyślna | Opis |
| ---- | ---- | ------------- | ----------- |
| IP_WHITELIST | ciąg | Puste | Podaj Rozdzielana średnikami lista adresów IP. Zawierać adresy IP maszyn, których pochodzą żądania obsługi, takie jak serwer NAS lub sieć VPN. Zakresy adresów IP to podsieci nie są obsługiwane. <br><br> Na przykład *10.0.0.1;10.0.0.2;10.0.0.3*.

Gdy nadejdzie żądanie z adresu IP, który istnieje dozwolone, weryfikację dwuetapową zostało pominięte. Lista dozwolonych adresów IP jest porównywany z adresu IP, który znajduje się w *ratNASIPAddress* atrybutu żądań RADIUS. Jeśli żądanie RADIUS jest dostarczany bez atrybutu ratNASIPAddress, są rejestrowane następujące ostrzeżenie: "Dozwolonych P_WHITE_LIST_WARNING::IP zostanie zignorowany ponieważ brakuje źródłowy adres IP, w żądaniu RADIUS w atrybucie NasIpAddress."

## <a name="next-steps"></a>Kolejne kroki

[Resolve error messages from the NPS extension for Azure Multi-Factor Authentication](howto-mfa-nps-extension-errors.md) (Rozstrzyganie komunikatów o błędach z rozszerzenia serwera NPS dotyczących usługi Azure Multi-Factor Authentication)