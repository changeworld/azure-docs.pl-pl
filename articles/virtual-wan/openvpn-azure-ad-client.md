---
title: 'Brama sieci VPN: klient sieci VPN dla połączeń protokołu OpenVPN P2S: uwierzytelnianie usługi Azure AD'
description: Za pomocą sieci VPN P2S można łączyć się z siecią wirtualną przy użyciu uwierzytelniania usługi Azure AD
services: vpn-gateway
author: anzaman
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 03/27/2020
ms.author: alzam
ms.openlocfilehash: edb509d43742aeecf74107ae8cb625aeafbccb9f
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2020
ms.locfileid: "80385625"
---
# <a name="configure-a-vpn-client-for-p2s-openvpn-protocol-connections-azure-ad-authentication"></a>Konfigurowanie klienta sieci VPN dla połączeń protokołu P2S OpenVPN: uwierzytelnianie usługi Azure AD

Ten artykuł ułatwia konfigurowanie klienta sieci VPN do łączenia się z siecią wirtualną przy użyciu sieci VPN typu lokacja i uwierzytelniania usługi Azure Active Directory. Przed nawiązaniem połączenia i uwierzytelnienia przy użyciu usługi Azure AD należy najpierw skonfigurować dzierżawę usługi Azure AD. Aby uzyskać więcej informacji, zobacz [Konfigurowanie dzierżawy usługi Azure AD](openvpn-azure-ad-tenant.md).

> [!NOTE]
> Uwierzytelnianie usługi Azure AD jest obsługiwane tylko dla połączeń protokołu OpenVPN®.
>

## <a name="working-with-client-profiles"></a><a name="profile"></a>Praca z profilami klientów

Aby nawiązać połączenie, należy pobrać klienta sieci VPN platformy Azure i skonfigurować profil klienta sieci VPN na każdym komputerze, który chce połączyć się z siecią wirtualną. Profil klienta można utworzyć na komputerze, wyeksportować, a następnie zaimportować na dodatkowe komputery.

### <a name="to-download-the-azure-vpn-client"></a>Aby pobrać klienta sieci VPN platformy Azure

Użyj tego [łącza,](https://go.microsoft.com/fwlink/?linkid=2117554) aby pobrać klienta sieci VPN platformy Azure. Upewnij się, że klient sieci VPN platformy Azure ma uprawnienia do uruchamiania w tle. Aby sprawdzić/włączyć uprawnienie, wykonaj poniższe czynności:

1. Przejdź do ekranu startowego , a następnie wybierz pozycję Ustawienia > aplikacje prywatności > w tle.
2. W obszarze Aplikacje w tle upewnij się, że **aplikacja Pozwól, aby aplikacje działały w tle,** była włączona.
3. W obszarze Wybierz aplikacje, które mogą być uruchamiane w tle, włącz ustawienia klienta sieci VPN platformy Azure **na Włączone**.

  ![Uprawnienia](./media/openvpn-azure-ad-client/backgroundpermission.png)

### <a name="to-create-a-certificate-based-client-profile"></a><a name="cert"></a>Aby utworzyć profil klienta opartego na certyfikatach

Podczas pracy z profilem opartym na certyfikatach upewnij się, że odpowiednie certyfikaty są zainstalowane na komputerze klienckim. Aby uzyskać więcej informacji o certyfikatach, zobacz [Instalowanie certyfikatów klientów](certificates-point-to-site.md).

  ![cert](./media/openvpn-azure-ad-client/create/create-cert1.jpg)

### <a name="to-create-a-radius-client-profile"></a><a name="radius"></a>Aby utworzyć profil klienta USŁUGI RADIUS

  ![Promień](./media/openvpn-azure-ad-client/create/create-radius1.jpg)
  
> [!NOTE]
> Klucz tajny serwera można wyeksportować w profilu klienta sieci VPN P2S.  Instrukcje dotyczące eksportowania profilu klienta można znaleźć [tutaj](about-vpn-profile-download.md).
>

### <a name="to-export-and-distribute-a-client-profile"></a><a name="export"></a>Aby wyeksportować i rozpowszechnić profil klienta

Gdy masz profil roboczy i musisz go rozesłać do innych użytkowników, możesz go wyeksportować, wykonując następujące czynności:

1. Wyróżnij profil klienta sieci VPN, który chcesz wyeksportować, wybierz **...**, a następnie wybierz pozycję **Eksportuj**.

    ![export](./media/openvpn-azure-ad-client/export/export1.jpg)

2. Wybierz lokalizację, w której chcesz zapisać ten profil, pozostaw nazwę pliku bez względu na to, w jakim się znajduje, a następnie wybierz pozycję **Zapisz,** aby zapisać plik xml.

    ![export](./media/openvpn-azure-ad-client/export/export2.jpg)

### <a name="to-import-a-client-profile"></a><a name="import"></a>Aby zaimportować profil klienta

1. Na stronie wybierz pozycję **Importuj**.

    ![import](./media/openvpn-azure-ad-client/import/import1.jpg)

2. Przejdź do pliku xml profilu i wybierz go. Po wybraniu wybranego pliku wybierz pozycję **Otwórz**.

    ![import](./media/openvpn-azure-ad-client/import/import2.jpg)

3. Określ nazwę profilu i wybierz pozycję **Zapisz**.

    ![import](./media/openvpn-azure-ad-client/import/import3.jpg)

4. Wybierz **pozycję Połącz,** aby połączyć się z siecią VPN.

    ![import](./media/openvpn-azure-ad-client/import/import4.jpg)

5. Po podłączeniu ikona zmieni kolor na zielony i na **powiedzmię Connected**.

    ![import](./media/openvpn-azure-ad-client/import/import5.jpg)

### <a name="to-delete-a-client-profile"></a><a name="delete"></a>Aby usunąć profil klienta

1. Wybierz wielokropek obok profilu klienta, który chcesz usunąć. Następnie wybierz pozycję **Usuń**.

    ![delete](./media/openvpn-azure-ad-client/delete/delete1.jpg)

2. Wybierz **pozycję Usuń,** aby usunąć.

    ![delete](./media/openvpn-azure-ad-client/delete/delete2.jpg)

## <a name="create-a-connection"></a><a name="connection"></a>Tworzenie połączenia

1. Na stronie wybierz **+** pozycję , a następnie **+ Dodaj**.

    ![połączenie](./media/openvpn-azure-ad-client/create/create1.jpg)

2. Wypełnij informacje o połączeniu. Jeśli nie masz pewności co do wartości, skontaktuj się z administratorem. Po wypełnieniu wartości wybierz pozycję **Zapisz**.

    ![połączenie](./media/openvpn-azure-ad-client/create/create2.jpg)

3. Wybierz **pozycję Połącz,** aby połączyć się z siecią VPN.

    ![połączenie](./media/openvpn-azure-ad-client/create/create3.jpg)

4. Wybierz odpowiednie poświadczenia, a następnie wybierz pozycję **Kontynuuj**.

    ![połączenie](./media/openvpn-azure-ad-client/create/create4.jpg)

5. Po pomyślnym nawiązaniu połączenia ikona zmieni kolor na zielony i na **powiedzmie Połącz .**

    ![połączenie](./media/openvpn-azure-ad-client/create/create5.jpg)

### <a name="to-connect-automatically"></a><a name="autoconnect"></a>Aby połączyć się automatycznie

Te kroki pomagają skonfigurować połączenie do automatycznego łączenia się z opcję Zawsze włączony.

1. Na stronie głównej klienta sieci VPN wybierz pozycję **Ustawienia sieci VPN**.

    ![auto](./media/openvpn-azure-ad-client/auto/auto1.jpg)

2. Wybierz **pozycję Tak** w polu dialogowym Przełączanie aplikacji.

    ![auto](./media/openvpn-azure-ad-client/auto/auto2.jpg)

3. Upewnij się, że połączenie, które chcesz ustawić, nie jest jeszcze połączone, a następnie zaznacz profil i zaznacz pole wyboru **Automatycznie połącz.**

    ![auto](./media/openvpn-azure-ad-client/auto/auto3.jpg)

4. Wybierz **pozycję Połącz,** aby zainicjować połączenie sieci VPN.

    ![auto](./media/openvpn-azure-ad-client/auto/auto4.jpg)

## <a name="diagnose-connection-issues"></a><a name="diagnose"></a>Diagnozowanie problemów z połączeniem

1. Aby zdiagnozować problemy z połączeniem, można użyć narzędzia **Diagnozowanie.** Wybierz **...** obok połączenia sieci VPN, które chcesz zdiagnozować, aby odsłonić menu. Następnie wybierz **polecenie Diagnozuj**.

    ![Zdiagnozować](./media/openvpn-azure-ad-client/diagnose/diagnose1.jpg)

2. Na stronie **Właściwości połączenia** wybierz pozycję **Uruchom diagnostykę**.

    ![Zdiagnozować](./media/openvpn-azure-ad-client/diagnose/diagnose2.jpg)

3. Zaloguj się przy użyciu poświadczeń.

    ![Zdiagnozować](./media/openvpn-azure-ad-client/diagnose/diagnose3.jpg)

4. Wyświetl wyniki diagnozy.

    ![Zdiagnozować](./media/openvpn-azure-ad-client/diagnose/diagnose4.jpg)

## <a name="faq"></a>Najczęściej zadawane pytania

### <a name="how-do-i-add-dns-suffixes-to-the-vpn-client"></a>Jak dodać sufiksy DNS do klienta sieci VPN?

Można zmodyfikować pobrany plik XML profilu i dodać ** \<dnssuffixes>\<dnssufix> \</dnssufix>\</dnssuffixes>** tagów

```
<azvpnprofile>
<clientconfig>

    <dnssuffixes>
          <dnssuffix>.mycorp.com</dnssuffix>
          <dnssuffix>.xyz.com</dnssuffix>
          <dnssuffix>.etc.net</dnssuffix>
    </dnssuffixes>
    
</clientconfig>
</azvpnprofile>
```

### <a name="how-do-i-add-custom-dns-servers-to-the-vpn-client"></a>Jak dodać niestandardowe serwery DNS do klienta sieci VPN?

Można zmodyfikować pobrany plik XML profilu i dodać ** \<dnsservers>\< \<dnsserver> /dnsserver>\</dnsservers>** tagi

```
<azvpnprofile>
<clientconfig>

    <dnsservers>
        <dnsserver>x.x.x.x</dnsserver>
        <dnsserver>y.y.y.y</dnsserver>
    </dnsservers>
    
</clientconfig>
</azvpnprofile>
```

> [!NOTE]
> Klient OpenVPN Azure AD wykorzystuje wpisy tabeli zasad rozpoznawania nazw DNS (NRPT), co `ipconfig /all`oznacza, że serwery DNS nie będą wyświetlane w pliku danych wyjściowym . Aby potwierdzić używane ustawienia DNS, zapoznaj się z [witryną Get-DnsClientNrptPolicy](https://docs.microsoft.com/powershell/module/dnsclient/get-dnsclientnrptpolicy?view=win10-ps) w programie PowerShell.
>

### <a name="how-do-i-add-custom-routes-to-the-vpn-client"></a>Jak dodać niestandardowe trasy do klienta sieci VPN?

Można zmodyfikować pobrany plik XML profilu i dodać ** \<otoki \<>trasy \<>\<maskę>docelową \<> \</miejsce docelowe>/maska />\</route>\</includeroutes>** tagów

```
<azvpnprofile>
<clientconfig>

    <includeroutes>
        <route>
            <destination>x.x.x.x</destination><mask>24</mask>
        </route>
    </includeroutes>
    
</clientconfig>
</azvpnprofile>
```

### <a name="how-do-i-block-exclude-routes-from-the-vpn-client"></a>Jak zablokować (wykluczyć) trasy z klienta sieci VPN?

Można zmodyfikować pobrany plik XML profilu i dodać ** \<wykluczenia \<>\<marszruty>\<maskę>docelową> \</miejsce docelowe>\</maska>\< \</trasa>/excluderoutes>** znaczników

```
<azvpnprofile>
<clientconfig>

    <excluderoutes>
        <route>
            <destination>x.x.x.x</destination><mask>24</mask>
        </route>
    </excluderoutes>
    
</clientconfig>
</azvpnprofile>
```

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji, zobacz [Tworzenie dzierżawy usługi Azure Active Directory dla połączeń P2S Open VPN korzystających z uwierzytelniania usługi Azure AD](openvpn-azure-ad-tenant.md).
