---
title: Połączenia klienta usług pulpitu zdalnego w Windows pulpitu wirtualnego — platformy Azure
description: Jak rozwiązywać problemy, podczas konfigurowania połączeń klientów w środowisku Windows pulpitu wirtualnego dzierżawy.
services: virtual-desktop
author: ChJenk
ms.service: virtual-desktop
ms.topic: troubleshoot
ms.date: 04/08/2019
ms.author: v-chjenk
ms.openlocfilehash: c5a67e22c301a2afc73a46a6def9a514426c497f
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/30/2019
ms.locfileid: "64928050"
---
# <a name="remote-desktop-client-connections"></a>Połączenia klienta usług pulpitu zdalnego

W tym artykule umożliwiają rozwiązywanie problemów z połączeniami klienta Windows pulpitu wirtualnego.

## <a name="provide-feedback"></a>Przekazywanie opinii

Firma Microsoft obecnie nie są zbyt przypadki pomocy technicznej Windows pulpitu wirtualnego jest dostępna w wersji zapoznawczej. Odwiedź stronę [społeczności technicznej pulpitu wirtualnego Windows](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) w celu omówienia usługi Windows pulpitu wirtualnego przy użyciu zespół pracujący nad produktem i elementów członkowskich aktywnej społeczności użytkowników.

## <a name="you-cant-open-a-web-client"></a>Nie można otworzyć klienta sieci web

Upewnij się, że istnieje połączenie z Internetem, otwierając innej witryny sieci web; na przykład [www.Bing.com](https://www.bing.com).

Użyj **nslookup** o potwierdzenie DNS mogą rozpoznać nazwę FQDN:

    ```cmd
    nslookup rdweb.wvd.microsoft.com
    ```

Spróbuj nawiązać połączenie z innym klientem, klient usług pulpitu zdalnego, Windows 7 lub systemu Windows 10 i wyboru zobaczyć, można otworzyć klienta sieci web.

### <a name="error-opening-another-site-fails"></a>Błąd: Otwieranie innej awarii lokacji

**Przyczyna:** Problemy z siecią i/lub awarii.

**Poprawka:** Skontaktuj się z działem pomocy technicznej w sieci.

### <a name="error-nslookup-cannot-resolve-the-name"></a>Błąd: Polecenie nslookup nie można rozpoznać nazwy

**Przyczyna:** Problemy z siecią i/lub awarii.

**Poprawka:** Skontaktuj się z działem pomocy technicznej w sieci

### <a name="error-you-cant-connect-but-other-clients-can-connect"></a>Błąd: Nie możesz się połączyć, ale inne klienci mogą łączyć się

**Przyczyna:** Przeglądarka nie zachowują się tak jak pracy z oczekiwaniami i jest zatrzymana.

**Poprawka:** Wykonaj te instrukcje, rozwiązywać problemy z przeglądarki.

1. Uruchom ponownie przeglądarkę.
2. Wyczyść pliki cookie. Zobacz [sposobu usuwania plików cookie w przeglądarce Internet Explorer](https://support.microsoft.com/help/278835/how-to-delete-cookie-files-in-internet-explorer).
3. Przeglądarka Wyczyść pamięć podręczną. Zobacz [Wyczyść pamięć podręczną przeglądarki w przeglądarce](https://binged.it/2RKyfdU).
4. Otwórz przeglądarkę w trybie prywatnym.

## <a name="web-client-stops-responding-or-disconnects"></a>Klient sieci Web przestaje odpowiadać lub odłącza

Spróbuj nawiązać połączenie przy użyciu innej przeglądarki lub klienta.

### <a name="error-other-browsers-and-clients-also-malfunction-or-fail-to-open"></a>Błąd: Innych przeglądarek i klientów również nieprawidłowe działanie lub utrata możliwości ich otwierania

**Przyczyna:** Problemy z systemem sieci i/lub operacji lub awarii

**Poprawka:** Zespoły, skontaktuj się z pomocą techniczną.

## <a name="web-client-keeps-prompting-for-credentials"></a>Klient sieci Web przechowuje monit o podanie poświadczeń

Jeśli klient sieci Web przechowuje monit o podanie poświadczeń, wykonaj te instrukcje.

1. Upewnij się, że adres URL sieci web klienta jest poprawny.
2. Upewnij się, że poświadczenia są środowiska pulpitu wirtualnego Windows powiązane z adresu URL.
3. Wyczyść pliki cookie. Zobacz [sposobu usuwania plików cookie w przeglądarce Internet Explorer](https://support.microsoft.com/help/278835/how-to-delete-cookie-files-in-internet-explorer).
4. Przeglądarka Wyczyść pamięć podręczną. Zobacz [Wyczyść pamięć podręczną przeglądarki w przeglądarce](https://binged.it/2RKyfdU).
5. Otwórz przeglądarkę w trybie prywatnym.

## <a name="remote-desktop-client-for-windows-7-or-windows-10-stops-responding-or-cannot-be-opened"></a>Klient pulpitu zdalnego dla Windows 7 lub Windows 10 nie odpowiada lub nie można otworzyć

Czyszczenie poza pasmem (OOB), klient rejestrów, należy użyć następujących poleceń cmdlet programu PowerShell.

```PowerShell
Remove-ItemProperty 'HKCU:\Software\Microsoft\Terminal Server Client\Default' - Name FeedURLs

#Remove RdClientRadc registry key
Remove-Item 'HKCU:\Software\Microsoft\RdClientRadc' -Recurse

#Remove all files under %appdata%\RdClientRadc
Remove-Item C:\Users\pavithir\AppData\Roaming\RdClientRadc\* -Recurse
```

Przejdź do **%AppData%\RdClientRadc** i Usuń całą zawartość.

Odinstalowanie i ponowne zainstalowanie klienta usług pulpitu zdalnego, Windows 7 i Windows 10.

## <a name="troubleshooting-end-user-connectivity"></a>Rozwiązywanie problemów z łącznością przez użytkownika końcowego

Czasami użytkownicy mogą uzyskiwać dostęp do ich źródła danych i zasobów lokalnych, ale nadal masz konfiguracji, dostępności lub problemy z wydajnością, które uniemożliwiają uzyskiwanie dostępu do zasobów zdalnych. W takich przypadkach użytkownik pobiera komunikaty podobne do następujących:

![Zdalne połączenia pulpitu komunikat o błędzie.](media/eb76b666808bddb611448dfb621152ce.png)

![Nie można nawiązać połączenia z bramy komunikat o błędzie.](media/a8fbb9910d4672147335550affe58481.png)

Wykonaj instrukcje dotyczące rozwiązywania problemów, te ogólne kody błędów połączenia klienta.

1. Upewnij się, nazwę użytkownika i czas, kiedy został problem.
2. Otwórz **PowerShell** i nawiązania połączenia z dzierżawą pulpitu wirtualnego Windows, gdy problem został zgłoszony.
3. Upewnij się, połączenie z poprawną dzierżawcy z **Get RdsTenant.**
4. Jeśli to konieczne, ustaw dzierżawy, kontekst grupy za pomocą **RdsContext zestaw — TenantGroupt\<TenantGroup\>**.
5. Za pomocą **Get RdsHostPool** i **Get RdsSessionHost** poleceń cmdlet, upewnij się, że rozwiązywania problemów jest wykonywana w puli właściwy host.
6. Wykonaj poniższe polecenie, aby uzyskać listę wszystkich działań nie powiodło się z typu połączenia określone okno czasu:

    ```cmd
     Get-RdsDiagnosticActivities -TenantName <TenantName> -username <UPN> -StartTime
     "11/21/2018 1:07:03 PM" -EndTime "11/21/2018 1:27:03 PM" -Outcome Failure -ActivityType Connection
    ```

7. Za pomocą **ActivityId** z poprzednich dane wyjściowe polecenia cmdlet, uruchom poniższe polecenie:

    ```
    (Get-RdsDiagnosticActivities -TenantName $tenant -ActivityId <ActivityId> -Detailed).Errors
    ```

8. Polecenie generuje dane wyjściowe podobne do danych wyjściowych poniżej. Użyj **ErrorCodeSymbolic** i **komunikat o błędzie** rozwiązywać problemy z główną przyczynę.

    ```
    ErrorSource       : <Source>
    ErrorOperation    : <Operation>
    ErrorCode         : <Error code>
    ErrorCodeSymbolic : <Error code string>
    ErrorMessage      : <Error code message>
    ErrorInternal     : <Internal for the OS>
    ReportedBy        : <Reported by component>
    Time              : <Timestampt>
    ```

### <a name="error-oaddusertogroupfailed--failed-to-add-user--username-to-group--remote-desktop-users-reason-win32errornosuchmember"></a>Błąd: O_ADD_USER_TO_GROUP_FAILED / nie można dodać użytkownika = ≤username≥ do grupy = Użytkownicy pulpitu zdalnego. Przyczyna: Win32.ERROR_NO_SUCH_MEMBER

**Przyczyna:** Maszyna wirtualna nie został przyłączony do domeny, w której się obiekt użytkownika.

**Poprawka:** Dodaj maszynę Wirtualną z odpowiednią domeną. Zobacz [dołączanie maszyny wirtualnej systemu Windows Server do domeny zarządzanej](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-admin-guide-join-windows-vm-portal).

### <a name="error-nslookup-cannot-resolve-the-name"></a>Błąd: Polecenie nslookup nie można rozpoznać nazwy

**Przyczyna:** Problemy z siecią lub awarii.

**Poprawka:** Skontaktuj się z działem pomocy technicznej w sieci

### <a name="error-connectionfailedclientprotocolerror"></a>Błąd: ConnectionFailedClientProtocolError

**Przyczyna:** Maszyny wirtualne użytkownik próbuje połączyć się z nie są przyłączone do domeny.

**Poprawka:** Dołącz do wszystkich maszyn wirtualnych, które są częścią puli hosta do kontrolera domeny.

## <a name="user-connects-but-nothing-is-displayed-no-feed"></a>Łączy się użytkownik, ale będą wyświetlane żadne informacje (bez podawania)

Użytkownik może uruchomić klientów usług pulpitu zdalnego i jest w stanie uwierzytelnić, jednak użytkownik nie widzi wszystkie ikony w odnajdywanie sieci web źródła danych.

Upewnij się, że użytkownik, zgłaszania problemów dotyczących przypisaniu do grupy aplikacji przy użyciu tego wiersza polecenia:

```cmd
Get-RdsAppGroupUser <tenantname> <hostpoolname> <appgroupname>
```

Upewnij się, że użytkownik jest rejestrowanie się przy użyciu poprawnych poświadczeń.

Jeśli klient sieci web jest używana, upewnij się, że nie występują żadne problemy buforowanych poświadczeń.

## <a name="next-steps"></a>Kolejne kroki

- Aby uzyskać ogólne na temat rozwiązywania problemów ścieżki eskalacji i pulpitu wirtualnego Windows zobacz [Rozwiązywanie problemów — omówienie, opinie i pomoc techniczna](troubleshoot-set-up-overview.md).
- Aby rozwiązywać problemy podczas tworzenia puli dzierżawy i hosta w środowisku Windows pulpitu wirtualnego, zobacz [dzierżawy i hostów puli tworzenia](troubleshoot-set-up-issues.md).
- Aby rozwiązywać problemy podczas konfigurowania maszyny wirtualnej (VM) w Windows pulpitu wirtualnego, zobacz [konfigurację maszyny wirtualnej hosta sesji](troubleshoot-vm-configuration.md).
- Aby rozwiązywać problemy podczas korzystania z programu PowerShell z usługą Windows pulpitu wirtualnego, zobacz [środowiska PowerShell pulpitu wirtualnego Windows](troubleshoot-powershell.md).
- Aby dowiedzieć się więcej na temat usługi w wersji zapoznawczej, zobacz [Windows Desktop w wersji zapoznawczej środowiska](https://docs.microsoft.com/azure/virtual-desktop/environment-setup?).
- Aby przejść przez samouczek rozwiązywania problemów, zobacz [samouczka: Rozwiązywanie problemów z wdrożeniami szablonu usługi Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-tutorial-troubleshoot).
- Aby dowiedzieć się więcej na temat inspekcji akcji, zobacz [inspekcji operacji przy użyciu usługi Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit).
- Aby dowiedzieć się więcej o akcjach, aby określić błędy podczas wdrażania, zobacz [wyświetlanie operacji wdrażania](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-operations).