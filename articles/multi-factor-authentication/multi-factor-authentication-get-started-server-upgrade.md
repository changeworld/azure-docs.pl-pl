---
title: "Uaktualnianie narzędzia PhoneFactor do serwera Azure MFA | Microsoft Docs"
description: Rozpoczynanie pracy z serwerem Azure MFA podczas uaktualniania ze starszej wersji agenta PhoneFactor.
services: multi-factor-authentication
documentationcenter: 
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: richagi
ms.assetid: 42838ff7-bdf2-4d06-bacc-b3839a00cd76
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 06/06/2017
ms.author: joflore
ms.openlocfilehash: a3451adfe19d547812b1450a9028a48a5fe2d727
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/11/2017
---
# <a name="upgrade-the-phonefactor-agent-to-azure-multi-factor-authentication-server"></a>Uaktualnianie agenta PhoneFactor do serwera Azure Multi-Factor Authentication
Aby uaktualnić agenta PhoneFactor (w wersji 5.x lub starszej) do serwera Azure Multi-Factor Authentication, najpierw odinstaluj agenta PhoneFactor i powiązane z nim składniki. Następnie można zainstalować serwer Multi-Factor Authentication i powiązane z nim składniki.

## <a name="uninstall-the-phonefactor-agent"></a>Odinstalowywanie agenta PhoneFactor

1. Najpierw należy utworzyć kopię zapasową pliku danych PhoneFactor. Domyślna lokalizacja instalacji to C:\Program Files\PhoneFactor\Data\Phonefactor.pfdata.

2. Jeśli zainstalowano portal użytkowników:
  1. Przejdź do folderu instalacji i utwórz kopię zapasową pliku web.config. Domyślna lokalizacja instalacji to C:\inetpub\wwwroot\PhoneFactor.

  2. Jeśli do portalu dodano niestandardowe kompozycje, należy wykonać kopię zapasową niestandardowego folderu poniżej katalogu C:\inetpub\wwwroot\PhoneFactor\App_Themes.

  3. Odinstaluj portal użytkowników za pośrednictwem agenta PhoneFactor (opcja dostępna tylko wtedy, gdy jest zainstalowany na tym samym serwerze co agent PhoneFactor) lub za pomocą modułu Programy i funkcje systemu Windows.

3. Jeśli zainstalowana jest usługa sieci Web aplikacji mobilnej:

  1. Przejdź do folderu instalacyjnego i utwórz kopię zapasową pliku web.config. Domyślna lokalizacja instalacji to C:\inetpub\wwwroot\PhoneFactorPhoneAppWebService.

  2. Odinstaluj usługę sieci Web aplikacji mobilnej za pośrednictwem modułu Programy i funkcje systemu Windows.

4. Jeśli zainstalowano zestaw SDK usługi sieci Web, należy go odinstalować za pomocą agenta PhoneFactor lub za pośrednictwem modułu Programy i funkcje systemu Windows.

5. Odinstaluj agenta PhoneFactor za pośrednictwem modułu Programy i funkcje systemu Windows.

## <a name="install-the-multi-factor-authentication-server"></a>Instalowanie serwera Multi-Factor Authentication

Ścieżka instalacji jest pobierana z rejestru z poprzedniej instalacji agenta PhoneFactor, dlatego instalacji należy dokonać w tej samej lokalizacji (np. C:\Program Files\PhoneFactor). Nowe instalacje mają inne domyślne ścieżki instalacji (np. C:\Program Files\Multi-Factor Authentication Server). Plik danych pozostawiony przez poprzedniego agenta PhoneFactor należy uaktualnić podczas instalacji, aby po zainstalowaniu nowego serwera Multi-Factor Authentication określeni wcześniej użytkownicy i ustawienia nadal były dostępne.

1. Jeśli zostanie wyświetlony monit, uaktywnij serwer Multi-Factor Authentication i upewnij się, że jest przypisany do odpowiedniej grupy replikacji.

2. Jeśli wcześniej zainstalowano zestaw SDK usługi sieci Web, należy zainstalować nowy zestaw SDK usługi sieci Web za pomocą interfejsu użytkownika serwera Multi-Factor Authentication.

  Domyślną nazwą katalogu wirtualnego jest teraz **MultiFactorAuthWebServiceSdk** zamiast **PhoneFactorWebServiceSdk**. Jeśli chcesz użyć poprzedniej nazwy, musisz podczas instalacji zmienić nazwę katalogu wirtualnego. W przeciwnym razie, jeśli zezwolisz procesowi instalacji na użycie nowej nazwy domyślnej, trzeba będzie zmienić adres URL w każdej aplikacji, która odwołuje się do zestawu SDK usługi internetowej, takiej jak portal użytkowników i usługa internetowa aplikacji mobilnej, aby wskazywała na poprawną lokalizację.

3. Jeśli wcześniej zainstalowano portal użytkowników na tym serwerze agenta PhoneFactor, zainstaluj nowy portal użytkowników usługi Multi-Factor Authentication za pomocą interfejsu użytkownika serwera Multi-Factor Authentication.

  Domyślną nazwą katalogu wirtualnego jest teraz **MultiFactorAuth** zamiast **PhoneFactor**. Jeśli chcesz użyć poprzedniej nazwy, musisz podczas instalacji zmienić nazwę katalogu wirtualnego. W przeciwnym razie, jeśli zezwolisz procesowi instalacji na użycie nowej nazwy domyślnej, musisz kliknąć ikonę Portal użytkowników na serwerze Multi-Factor Authentication i zaktualizować adres URL portalu użytkowników na karcie Ustawienia.

4. Jeśli portal użytkowników i/lub usługa sieci Web aplikacji mobilnej zostały wcześniej zainstalowane na innym serwerze z agenta PhoneFactor:

  1. Przejdź do lokalizacji instalacji (np. C:\Program Files\PhoneFactor) i skopiuj jeden lub więcej plików instalacyjnych na inny serwer. Zarówno dla portalu użytkowników, jak i dla usługi sieci Web aplikacji mobilnej istnieją instalatory w wersji 32-bitowej i 64-bitowej. Mają one nazwy MultiFactorAuthenticationUserPortalSetupXX.msi i MultiFactorAuthenticationMobileAppWebServiceSetupXX.msi.

  2. Aby zainstalować portal użytkowników na serwerze sieci Web, otwórz wiersz polecenia jako administrator i uruchom instalatora MultiFactorAuthenticationUserPortalSetupXX.msi.

    Domyślną nazwą katalogu wirtualnego jest teraz **MultiFactorAuth** zamiast **PhoneFactor**. Jeśli chcesz użyć poprzedniej nazwy, musisz podczas instalacji zmienić nazwę katalogu wirtualnego. W przeciwnym razie, jeśli zezwolisz procesowi instalacji na użycie nowej nazwy domyślnej, musisz kliknąć ikonę Portal użytkowników na serwerze Multi-Factor Authentication i zaktualizować adres URL portalu użytkowników na karcie Ustawienia. Istniejących użytkowników trzeba poinformować o nowym adresie URL.

  3. Przejdź do lokalizacji instalacji portalu użytkowników (np. C:\inetpub\wwwroot\MultiFactorAuth) i otwórz do edycji plik web.config. Z oryginalnego pliku web.config, który został umieszczony w kopii zapasowej przed uaktualnieniem, skopiuj wartości z sekcji appSettings i applicationSettings do nowego pliku web.config. Jeśli nowa domyślna nazwa katalogu wirtualnego została zachowana podczas instalacji zestawu SDK usługi sieci Web, zmień adres URL w sekcji applicationSettings, aby wskazywał poprawną lokalizację. Jeśli w poprzednim pliku web.config zostały zmienione inne wartości domyślne, zastosuj te same zmiany w nowym pliku web.config.

  4. Aby zainstalować usługę sieci Web aplikacji mobilnej na serwerze sieci Web, otwórz wiersz polecenia jako administrator i uruchom instalatora MultiFactorAuthenticationMobileAppWebServiceSetupXX.msi.

    Domyślną nazwą katalogu wirtualnego jest teraz **MultiFactorAuthMobileAppWebService** zamiast **PhoneFactorPhoneAppWebService**. Jeśli chcesz użyć poprzedniej nazwy, musisz podczas instalacji zmienić nazwę katalogu wirtualnego. Można wybrać krótszą nazwę, aby ułatwić użytkownikom końcowym wpisywanie jej w urządzeniach przenośnych. W przeciwnym razie, jeśli zezwolisz procesowi instalacji na użycie nowej nazwy domyślnej, kliknij ikonę aplikację mobilnej na serwerze Multi-Factor Authentication i zaktualizuj adres URL usługi sieci Web aplikacji mobilnej.

  5. Przejdź do lokalizacji instalacji usługi internetowej aplikacji mobilnej (np. C:\inetpub\wwwroot\MultiFactorAuthMobileAppWebService) i otwórz do edycji plik web.config. Z oryginalnego pliku web.config, który został umieszczony w kopii zapasowej przed uaktualnieniem, skopiuj wartości z sekcji appSettings i applicationSettings do nowego pliku web.config. Jeśli nowa domyślna nazwa katalogu wirtualnego została zachowana podczas instalacji zestawu SDK usługi sieci Web, zmień adres URL w sekcji applicationSettings, aby wskazywał poprawną lokalizację. Jeśli w poprzednim pliku web.config zostały zmienione inne wartości domyślne, zastosuj te same zmiany w nowym pliku web.config.

## <a name="next-steps"></a>Następne kroki

- [Zainstaluj portal użytkowników](multi-factor-authentication-get-started-portal.md) dla serwera Azure Multi-Factor Authentication.

- [Skonfiguruj uwierzytelnianie systemu Windows](multi-factor-authentication-get-started-server-windows.md) dla aplikacji. 
