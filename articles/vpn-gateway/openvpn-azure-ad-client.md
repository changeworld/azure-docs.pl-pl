---
title: 'VPN Gateway: klient VPN dla połączeń protokołu OpenVPN Protocol P2S: uwierzytelnianie usługi Azure AD'
description: Aby nawiązać połączenie z siecią wirtualną przy użyciu uwierzytelniania usługi Azure AD, możesz użyć sieci VPN P2S
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 02/28/2020
ms.author: alzam
ms.openlocfilehash: fc48b0ae9cf4162b4b9abba14c6e909ca091fd23
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2020
ms.locfileid: "78251612"
---
# <a name="configure-a-vpn-client-for-p2s-openvpn-protocol-connections-azure-ad-authentication"></a>Konfigurowanie klienta VPN dla połączeń protokołu P2S OpenVPN: uwierzytelnianie usługi Azure AD

Ten artykuł pomaga skonfigurować klienta sieci VPN do łączenia się z siecią wirtualną przy użyciu sieci VPN typu punkt-lokacja i uwierzytelniania Azure Active Directory. Aby można było nawiązać połączenie i uwierzytelnić się za pomocą usługi Azure AD, należy najpierw skonfigurować dzierżawę usługi Azure AD. Aby uzyskać więcej informacji, zobacz [Konfigurowanie dzierżawy usługi Azure AD](openvpn-azure-ad-tenant.md).

> [!NOTE]
> Uwierzytelnianie usługi Azure AD jest obsługiwane tylko dla połączeń protokołu OpenVPN®.
>

## <a name="profile"></a>Praca z profilami klientów

Aby nawiązać połączenie, należy pobrać klienta sieci VPN platformy Azure i skonfigurować profil klienta sieci VPN na każdym komputerze, który chce nawiązać połączenie z siecią wirtualną. Można utworzyć profil klienta na komputerze, wyeksportować go, a następnie zaimportować do dodatkowych komputerów.

### <a name="to-download-the-azure-vpn-client"></a>Aby pobrać klienta sieci VPN platformy Azure

Użyj tego [linku](https://go.microsoft.com/fwlink/?linkid=2117554) , aby pobrać klienta sieci VPN platformy Azure.

### <a name="cert"></a>Aby utworzyć profil klienta oparty na certyfikacie

Podczas pracy z profilem opartym na certyfikatach upewnij się, że odpowiednie certyfikaty są zainstalowane na komputerze klienckim. Aby uzyskać więcej informacji o certyfikatach, zobacz [Instalowanie certyfikatów klienta](point-to-site-how-to-vpn-client-install-azure-cert.md).

  ![zarchiwizowan](./media/openvpn-azure-ad-client/create/create-cert1.jpg)

### <a name="radius"></a>Aby utworzyć profil klienta usługi RADIUS

  ![promienie](./media/openvpn-azure-ad-client/create/create-radius1.jpg)
  
> [!NOTE]
> Wpis tajny serwera można wyeksportować w profilu klienta sieci VPN P2S.  Instrukcje dotyczące sposobu eksportowania profilu klienta można znaleźć [tutaj](about-vpn-profile-download.md).
>

### <a name="export"></a>Aby wyeksportować i rozpowszechnić profil klienta

Gdy dysponujesz profilem roboczym i chcesz go rozesłać do innych użytkowników, możesz go wyeksportować, wykonując następujące czynności:

1. Zaznacz profil klienta sieci VPN, który chcesz wyeksportować, wybierz pozycję **...** , a następnie wybierz pozycję **Eksportuj**.

    ![wywozu](./media/openvpn-azure-ad-client/export/export1.jpg)

2. Wybierz lokalizację, w której chcesz zapisać ten profil, pozostaw nazwę pliku, a następnie wybierz pozycję **Zapisz** , aby zapisać plik XML.

    ![wywozu](./media/openvpn-azure-ad-client/export/export2.jpg)

### <a name="import"></a>Aby zaimportować profil klienta

1. Na stronie wybierz pozycję **Importuj**.

    ![zaimportować](./media/openvpn-azure-ad-client/import/import1.jpg)

2. Przejdź do pliku XML profilu i wybierz go. Po wybraniu pliku wybierz pozycję **Otwórz**.

    ![zaimportować](./media/openvpn-azure-ad-client/import/import2.jpg)

3. Określ nazwę profilu i wybierz pozycję **Zapisz**.

    ![zaimportować](./media/openvpn-azure-ad-client/import/import3.jpg)

4. Wybierz pozycję **Połącz** , aby nawiązać połączenie z siecią VPN.

    ![zaimportować](./media/openvpn-azure-ad-client/import/import4.jpg)

5. Po nawiązaniu połączenia ikona zmieni kolor na zielony i powiedzie **się.**

    ![zaimportować](./media/openvpn-azure-ad-client/import/import5.jpg)

### <a name="delete"></a>Aby usunąć profil klienta

1. Wybierz wielokropek obok profilu klienta, który chcesz usunąć. Następnie wybierz pozycję **Usuń**.

    ![delete](./media/openvpn-azure-ad-client/delete/delete1.jpg)

2. Wybierz pozycję **Usuń** , aby usunąć.

    ![delete](./media/openvpn-azure-ad-client/delete/delete2.jpg)

## <a name="connection"></a>Utwórz połączenie

1. Na stronie wybierz **+** , a następnie pozycję **+ Dodaj**.

    ![połączenie](./media/openvpn-azure-ad-client/create/create1.jpg)

2. Wprowadź informacje o połączeniu. Jeśli nie masz pewności co do wartości, skontaktuj się z administratorem. Po wypełnieniu wartości wybierz pozycję **Zapisz**.

    ![połączenie](./media/openvpn-azure-ad-client/create/create2.jpg)

3. Wybierz pozycję **Połącz** , aby nawiązać połączenie z siecią VPN.

    ![połączenie](./media/openvpn-azure-ad-client/create/create3.jpg)

4. Wybierz odpowiednie poświadczenia, a następnie wybierz pozycję **Kontynuuj**.

    ![połączenie](./media/openvpn-azure-ad-client/create/create4.jpg)

5. Po pomyślnym nawiązaniu połączenia ikona zmieni kolor na zielony i powiedzie **się.**

    ![połączenie](./media/openvpn-azure-ad-client/create/create5.jpg)

### <a name="autoconnect"></a>Aby nawiązać połączenie automatycznie

Te kroki ułatwiają skonfigurowanie połączenia do automatycznego łączenia z usługą Always-On.

1. Na stronie głównej klienta sieci VPN wybierz pozycję **Ustawienia sieci VPN**.

    ![auto](./media/openvpn-azure-ad-client/auto/auto1.jpg)

2. W oknie dialogowym przełączanie aplikacji wybierz pozycję **tak** .

    ![auto](./media/openvpn-azure-ad-client/auto/auto2.jpg)

3. Upewnij się, że połączenie, które chcesz ustawić, nie jest już połączone, a następnie zaznacz profil i zaznacz pole wyboru **Połącz automatycznie** .

    ![auto](./media/openvpn-azure-ad-client/auto/auto3.jpg)

4. Wybierz pozycję **Połącz** , aby zainicjować połączenie sieci VPN.

    ![auto](./media/openvpn-azure-ad-client/auto/auto4.jpg)

## <a name="diagnose"></a>Diagnozuj problemy z połączeniem

1. Aby zdiagnozować problemy z połączeniem, można użyć narzędzia do **diagnozowania** . Wybierz **...** obok połączenia sieci VPN, które chcesz zdiagnozować, aby wyświetlić menu. Następnie wybierz pozycję **Diagnozuj**.

    ![Sprawdź](./media/openvpn-azure-ad-client/diagnose/diagnose1.jpg)

2. Na stronie **Właściwości połączenia** wybierz pozycję **Uruchom diagnostykę**.

    ![Sprawdź](./media/openvpn-azure-ad-client/diagnose/diagnose2.jpg)

3. Zaloguj się przy użyciu swoich poświadczeń.

    ![Sprawdź](./media/openvpn-azure-ad-client/diagnose/diagnose3.jpg)

4. Wyświetl wyniki diagnostyki.

    ![Sprawdź](./media/openvpn-azure-ad-client/diagnose/diagnose4.jpg)

## <a name="faq"></a>Często zadawane pytania

### <a name="how-do-i-add-dns-suffixes-to-the-vpn-client"></a>Jak mogę dodać sufiksy DNS do klienta sieci VPN?

Można zmodyfikować pobrany plik XML profilu i dodać **\<dnssuffixes >\<dnssufix > \</dnssufix >\</dnssuffixes >** Tagi

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

### <a name="how-do-i-add-custom-dns-servers-to-the-vpn-client"></a>Jak mogę dodać do klienta sieci VPN niestandardowe serwery DNS?

Można zmodyfikować pobrany plik XML profilu i dodać **\<dnsservers >\<serwer dns > \</dnsserver >\</dnsservers >** Tagi

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

### <a name="how-do-i-add-custom-routes-to-the-vpn-client"></a>Jak mogę dodać trasy niestandardowe do klienta sieci VPN?

Można zmodyfikować pobrany plik XML profilu i dodać **\<includeroutes >\<route >\<destination >\<mask > \</destination >\</mask >\</route >\</includeroutes >** Tagi

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

### <a name="how-do-i-block-exclude-routes-from-the-vpn-client"></a>Jak mogę blokowe (wykluczanie) tras z klienta sieci VPN?

Można zmodyfikować pobrany plik XML profilu i dodać **\<excluderoutes >\<route >\<destination >\<mask > \</destination >\</mask >\</route >\</excluderoutes >** Tagi

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

Aby uzyskać więcej informacji, zobacz [Tworzenie dzierżawy Azure Active Directory dla P2S Otwórz połączenia sieci VPN korzystające z uwierzytelniania usługi Azure AD](openvpn-azure-ad-tenant.md).
