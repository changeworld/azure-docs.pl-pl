---
title: Pulpit zdalny połączenia klientów w programie Virtual Desktop systemu Windows — Azure
description: Jak rozwiązywać problemy podczas konfigurowania połączeń klienta w środowisku dzierżawy pulpitu wirtualnego systemu Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 04/08/2019
ms.author: helohr
ms.openlocfilehash: c6c7a57a2093445d3922f9349242c9a902df7370
ms.sourcegitcommit: 29880cf2e4ba9e441f7334c67c7e6a994df21cfe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/26/2019
ms.locfileid: "71300720"
---
# <a name="remote-desktop-client-connections"></a>Połączenia klienta pulpitu zdalnego

Ten artykuł służy do rozwiązywania problemów z połączeniami klienta pulpitu wirtualnego systemu Windows.

## <a name="provide-feedback"></a>Przekazywanie opinii

Obecnie nie zajmują się pomocą techniczną, gdy pulpit wirtualny systemu Windows jest w wersji zapoznawczej. Odwiedź [społeczność Tech. pulpitu wirtualnego systemu Windows](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) , aby omówić usługę pulpitu wirtualnego systemu Windows z zespołem produktu i aktywnymi członkami społeczności.

## <a name="you-cant-open-a-web-client"></a>Nie można otworzyć klienta sieci Web

Upewnij się, że masz połączenie z Internetem, otwierając inną witrynę sieci Web; na przykład [www.Bing.com](https://www.bing.com).

Aby potwierdzić, że serwer DNS może rozpoznać nazwę FQDN, użyj **polecenia nslookup** :

```cmd
nslookup rdweb.wvd.microsoft.com
```

Spróbuj nawiązać połączenie z innym klientem, takim jak Pulpit zdalny Client dla systemu Windows 7 lub Windows 10, i sprawdź, czy można otworzyć klienta sieci Web.

### <a name="error-opening-another-site-fails"></a>Błąd: Otwieranie innej lokacji nie powiodło się

**Może** Problemy z siecią i/lub przestoje.

**Wiązane** Skontaktuj się z pomocą techniczną.

### <a name="error-nslookup-cannot-resolve-the-name"></a>Błąd: Narzędzie Nslookup nie może rozpoznać nazwy

**Może** Problemy z siecią i/lub przestoje.

**Wiązane** Skontaktuj się z pomocą techniczną sieci

### <a name="error-you-cant-connect-but-other-clients-can-connect"></a>Błąd: Nie można nawiązać połączenia, ale inni klienci mogą nawiązywać połączenia

**Może** Przeglądarka nie zachowuje się zgodnie z oczekiwaniami i przestanie działać.

**Wiązane** Postępuj zgodnie z tymi instrukcjami, aby rozwiązać problem z przeglądarką.

1. Uruchom ponownie przeglądarkę.
2. Wyczyść pliki cookie przeglądarki. Zobacz [Jak usunąć pliki cookie w programie Internet Explorer](https://support.microsoft.com/help/278835/how-to-delete-cookie-files-in-internet-explorer).
3. Wyczyść pamięć podręczną przeglądarki. Zobacz [Wyczyść pamięć podręczną przeglądarki w przeglądarce](https://binged.it/2RKyfdU).
4. Otwórz przeglądarkę w trybie prywatnym.

## <a name="web-client-stops-responding-or-disconnects"></a>Klient sieci Web przestaje odpowiadać lub rozłącza

Spróbuj połączyć się przy użyciu innej przeglądarki lub klienta.

### <a name="error-other-browsers-and-clients-also-malfunction-or-fail-to-open"></a>Błąd: Inne przeglądarki i klienci również działają nieprawidłowo lub nie mogą otworzyć

**Może** Problemy lub awarie systemu operacyjnego

**Wiązane** Skontaktuj się z zespołami pomocy technicznej.

## <a name="web-client-keeps-prompting-for-credentials"></a>Klient sieci Web ciągle wyświetla monit o poświadczenia

Jeśli klient sieci Web wyświetla monit o podanie poświadczeń, wykonaj te instrukcje.

1. Upewnij się, że adres URL klienta sieci Web jest prawidłowy.
2. Upewnij się, że poświadczenia środowiska pulpitu wirtualnego systemu Windows są powiązane z adresem URL.
3. Wyczyść pliki cookie przeglądarki. Zobacz [Jak usunąć pliki cookie w programie Internet Explorer](https://support.microsoft.com/help/278835/how-to-delete-cookie-files-in-internet-explorer).
4. Wyczyść pamięć podręczną przeglądarki. Zobacz [Wyczyść pamięć podręczną przeglądarki w przeglądarce](https://binged.it/2RKyfdU).
5. Otwórz przeglądarkę w trybie prywatnym.

## <a name="remote-desktop-client-for-windows-7-or-windows-10-stops-responding-or-cannot-be-opened"></a>Klient Pulpit zdalny dla systemu Windows 7 lub Windows 10 przestaje odpowiadać lub nie może zostać otwarty

Użyj następujących poleceń cmdlet programu PowerShell, aby wyczyścić rejestry klientów poza pasmem (OOB).

```PowerShell
Remove-ItemProperty 'HKCU:\Software\Microsoft\Terminal Server Client\Default' - Name FeedURLs

#Remove RdClientRadc registry key
Remove-Item 'HKCU:\Software\Microsoft\RdClientRadc' -Recurse

#Remove all files under %appdata%\RdClientRadc
Remove-Item C:\Users\pavithir\AppData\Roaming\RdClientRadc\* -Recurse
```

Przejdź do **%AppData%\RdClientRadc** i Usuń całą zawartość.

Odinstaluj i ponownie zainstaluj program Pulpit zdalny Client dla systemu Windows 7 i Windows 10.

## <a name="troubleshooting-end-user-connectivity"></a>Rozwiązywanie problemów z łącznością użytkowników końcowych

Czasami użytkownicy mogą uzyskać dostęp do swoich źródeł danych i zasobów lokalnych, ale nadal mają problemy z konfiguracją, dostępnością lub wydajnością, które uniemożliwiają im dostęp do zdalnych zasobów. W takich przypadkach użytkownik otrzymuje komunikaty podobne do następujących:

![Podłączanie pulpitu zdalnego komunikat o błędzie.](media/eb76b666808bddb611448dfb621152ce.png)

![Nie można nawiązać połączenia z komunikatem o błędzie bramy.](media/a8fbb9910d4672147335550affe58481.png)

Postępuj zgodnie z tymi ogólnymi instrukcjami dotyczącymi rozwiązywania problemów z błędami połączenia klienta.

1. Potwierdź nazwę użytkownika i godzinę napotkania problemu.
2. Otwórz program **PowerShell** i nawiąż połączenie z dzierżawą pulpitu wirtualnego systemu Windows, w której został zgłoszony problem.
3. Potwierdź połączenie z poprawną dzierżawą za pomocą **Get-RdsTenant.**
4. Za pomocą poleceń cmdlet **Get-RdsHostPool** i **Get-RdsSessionHost** upewnij się, że Rozwiązywanie problemów odbywa się w poprawnej puli hostów.
5. Wykonaj poniższe polecenie, aby uzyskać listę wszystkich zakończonych niepowodzeniem działań typu połączenie dla określonego przedziału czasu:

    ```PowerShell
     Get-RdsDiagnosticActivities -TenantName <TenantName> -username <UPN> -StartTime
     "11/21/2018 1:07:03 PM" -EndTime "11/21/2018 1:27:03 PM" -Outcome Failure -ActivityType Connection
    ```

6. Korzystając z **ActivityId** z poprzedniego danych wyjściowych polecenia cmdlet, uruchom następujące polecenie:

    ```PowerShell
    (Get-RdsDiagnosticActivities -TenantName $tenant -ActivityId <ActivityId> -Detailed).Errors
    ```

7. Polecenie generuje dane wyjściowe podobne do danych wyjściowych przedstawionych poniżej. Użyj **ErrorCodeSymbolic** i **ErrorMessage** , aby rozwiązać problem z główną przyczyną.

    ```PowerShell
    ErrorSource       : <Source>
    ErrorOperation    : <Operation>
    ErrorCode         : <Error code>
    ErrorCodeSymbolic : <Error code string>
    ErrorMessage      : <Error code message>
    ErrorInternal     : <Internal for the OS>
    ReportedBy        : <Reported by component>
    Time              : <Timestampt>
    ```

### <a name="error-o_add_user_to_group_failed--failed-to-add-user--username-to-group--remote-desktop-users-reason-win32error_no_such_member"></a>Błąd: O_ADD_USER_TO_GROUP_FAILED/nie można dodać użytkownika = ≤ nazwę użytkownika ≥ do grupy = Pulpit zdalny użytkownicy. Dotycząca Win32.ERROR_NO_SUCH_MEMBER

**Może** Maszyna wirtualna nie została przyłączona do domeny, w której znajduje się obiekt użytkownika.

**Wiązane** Dodaj maszynę wirtualną do odpowiedniej domeny. Zobacz [przyłączanie maszyny wirtualnej z systemem Windows Server do domeny zarządzanej](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-admin-guide-join-windows-vm-portal).

### <a name="error-nslookup-cannot-resolve-the-name"></a>Błąd: Narzędzie Nslookup nie może rozpoznać nazwy

**Może** Problemy z siecią lub awarie.

**Wiązane** Skontaktuj się z pomocą techniczną sieci

### <a name="error-connectionfailedclientprotocolerror"></a>Błąd: ConnectionFailedClientProtocolError

**Może** Maszyny wirtualne, z którymi użytkownik próbuje nawiązać połączenie, nie są przyłączone do domeny.

**Wiązane** Dołącz wszystkie maszyny wirtualne, które są częścią puli hostów, do kontrolera domeny.

### <a name="error-connectionfailedusersidinformationmismatch"></a>Błąd: ConnectionFailedUserSIDInformationMismatch
**Może** Identyfikator SID z tokenu Azure Active Directory użytkownika (AD) nie jest zgodny z identyfikatorem SID zwróconym przez kontroler domeny podczas próby włączenia użytkownika do zdalnego logowania. Ten błąd zazwyczaj występuje podczas próby zalogowania się do środowiska Azure Active Directory Domain Services (Azure AD DS) przy użyciu użytkownika pierwotnie źródłowego z usługi AD systemu Windows Server.

**Wiązane** Ten scenariusz nie jest obecnie obsługiwany. Tylko użytkownicy zarejestrowani z Azure Active Directory mogą logować się do maszyn wirtualnych z systemem Windows, które są połączone z usługą Azure AD DS.

## <a name="user-connects-but-nothing-is-displayed-no-feed"></a>Użytkownik nawiązuje połączenie, ale nic nie jest wyświetlane (brak źródła danych)

Użytkownik może uruchomić Pulpit zdalny klientów i może się uwierzytelnić, jednak użytkownik nie zobaczy żadnych ikon w źródle odnajdywania sieci Web.

Upewnij się, że użytkownik zgłaszał problemy, został przypisany do grup aplikacji przy użyciu tego wiersza polecenia:

```PowerShell
Get-RdsAppGroupUser <tenantname> <hostpoolname> <appgroupname>
```

Upewnij się, że użytkownik loguje się przy użyciu poprawnych poświadczeń.

Jeśli klient sieci Web jest używany, upewnij się, że nie występują problemy z buforowanymi poświadczeniami.

## <a name="next-steps"></a>Następne kroki

- Aby zapoznać się z omówieniem rozwiązywania problemów z pulpitem wirtualnym systemu Windows i ścieżkami eskalacji, zobacz [Omówienie rozwiązywania problemów, opinie i pomoc techniczna](troubleshoot-set-up-overview.md).
- Aby rozwiązać problemy podczas tworzenia dzierżawy i puli hostów w środowisku pulpitu wirtualnego systemu Windows, zobacz [Tworzenie dzierżawy i puli hostów](troubleshoot-set-up-issues.md).
- Aby rozwiązać problemy podczas konfigurowania maszyny wirtualnej w programie Virtual Desktop systemu Windows, zobacz [Konfiguracja maszyny wirtualnej hosta sesji](troubleshoot-vm-configuration.md).
- Aby rozwiązać problemy występujące podczas korzystania z programu PowerShell z pulpitem wirtualnym systemu Windows, zobacz [Windows Virtual Desktop PowerShell](troubleshoot-powershell.md).
- Aby dowiedzieć się więcej na temat usługi w wersji zapoznawczej, zobacz [środowisko Windows Virtual Desktop Preview](https://docs.microsoft.com/azure/virtual-desktop/environment-setup?).
- Aby przejść przez samouczek dotyczący rozwiązywania [problemów, zobacz Samouczek: Rozwiązywanie problemów z](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-tutorial-troubleshoot)wdrożeniami szablonów Menedżer zasobów.
- Aby dowiedzieć się więcej o akcjach inspekcji, zobacz [Inspekcja operacji przy użyciu Menedżer zasobów](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit).
- Aby dowiedzieć się więcej o akcjach dotyczących określania błędów podczas wdrażania, zobacz [Wyświetlanie operacji wdrażania](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-operations).
