---
title: Połącz się prywatnie z aplikacją internetową przy użyciu prywatnego punktu końcowego platformy Azure
description: Połącz się prywatnie z aplikacją internetową przy użyciu prywatnego punktu końcowego platformy Azure
author: ericgre
ms.assetid: b8c5c7f8-5e90-440e-bc50-38c990ca9f14
ms.topic: article
ms.date: 03/12/2020
ms.author: ericg
ms.service: app-service
ms.workload: web
ms.openlocfilehash: bb78536326885e043279de1ff77e6e8efcd95193
ms.sourcegitcommit: b8d0d72dfe8e26eecc42e0f2dbff9a7dd69d3116
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/10/2020
ms.locfileid: "79037147"
---
# <a name="connect-privately-to-a-web-app-using-azure-private-endpoint-preview"></a>Połącz się prywatnie z aplikacją internetową przy użyciu prywatnego punktu końcowego platformy Azure (wersja zapoznawcza)

Prywatny punkt końcowy platformy Azure to podstawowy blok konstrukcyjny dla prywatnego linku na platformie Azure. Pozwala ona na nawiązywanie połączeń prywatnych z aplikacją sieci Web.
W tym przewodniku szybki start dowiesz się, jak wdrożyć aplikację internetową za pomocą prywatnego punktu końcowego i połączyć się z tą aplikacją sieci Web z poziomu maszyny wirtualnej.

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do witryny Azure Portal pod adresem https://portal.azure.com.

## <a name="virtual-network-and-virtual-machine"></a>Sieć wirtualna i maszyna wirtualna

W tej sekcji utworzysz sieć wirtualną i podsieć do hostowania maszyny wirtualnej, która jest używana do uzyskiwania dostępu do aplikacji sieci Web za pomocą prywatnego punktu końcowego.

### <a name="create-the-virtual-network"></a>Tworzenie sieci wirtualnej

W tej sekcji utworzysz sieć wirtualną i podsieć.

1. W lewym górnym rogu ekranu wybierz pozycję **Utwórz zasób** > **sieci** > **sieci wirtualnej** lub Wyszukaj **sieć wirtualną** w polu wyszukiwania.

1. W obszarze **Utwórz sieć wirtualną**wprowadź lub wybierz te informacje na karcie podstawowe:

 ![Utwórz Virtual Network][1]

1. Kliknij przycisk **"dalej: adresy IP >"** i wprowadź lub wybierz następujące informacje:

![Skonfiguruj adresy IP][2]

1. W sekcji podsieć kliknij pozycję **"+ Dodaj podsieć"** i wprowadź poniższe informacje, a następnie kliknij pozycję **"Dodaj"** .

![Dodaj podsieć][3]

1. Kliknij pozycję **"Przejrzyj + Utwórz"**

1. Po przekazaniu walidacji kliknij pozycję **"Utwórz"** .

### <a name="create-virtual-machine"></a>Tworzenie maszyny wirtualnej

1. W lewym górnym rogu ekranu w Azure Portal wybierz pozycję **Utwórz zasób** > **obliczeniowe** > **maszynę wirtualną**

1. W obszarze Tworzenie maszyny wirtualnej — podstawy wprowadź lub wybierz następujące informacje:

![Podstawowa maszyna wirtualna ][4]

1. Wybierz pozycję **"dalej: dyski"**

Zachowaj ustawienia domyślne.

1. Wybierz pozycję **"dalej: sieć"** , wybierz następujące informacje:

![Sieć ][5]

1. Kliknij pozycję **"Przejrzyj + Utwórz"**

1. Po przekazaniu walidacji komunikatu kliknij przycisk **"Utwórz"** .

## <a name="create-your-web-app-and-private-endpoint"></a>Tworzenie aplikacji sieci Web i prywatnego punktu końcowego

W tej sekcji utworzysz prywatną aplikację sieci Web przy użyciu prywatnego punktu końcowego.

> [!Note]
>Funkcja prywatnego punktu końcowego jest dostępna tylko dla wersji Premium v2 i jest izolowana z zewnętrzną jednostką SKU środowiska ASE

### <a name="web-app"></a>Aplikacja internetowa

1. W lewym górnym rogu ekranu w Azure Portal wybierz pozycję **Utwórz zasób** > **Internet** > Web **App**

1. W obszarze Tworzenie aplikacji sieci Web — podstawowe wprowadź lub wybierz następujące informacje:

![Aplikacja sieci Web — podstawowa ][6]

1. Wybierz pozycję **"Przejrzyj i Utwórz"**

1. Po przekazaniu walidacji komunikatu kliknij przycisk **"Utwórz"** .

### <a name="create-the-private-endpoint"></a>Tworzenie prywatnego punktu końcowego

1. We właściwościach aplikacji sieci Web wybierz pozycję **ustawienia** > **Sieć** i kliknij pozycję **"Konfigurowanie połączeń prywatnych punktów końcowych"** .

![Sieć aplikacji sieci Web][7]

1. W Kreatorze kliknij pozycję **"+ Dodaj"**

![Prywatny punkt końcowy aplikacji sieci Web][8]

1. Wypełnij informacje dotyczące subskrypcji, sieci wirtualnej i podsieci, a następnie kliknij przycisk **OK** .

![Sieć aplikacji sieci Web][9]

1. Przegląd tworzenia prywatnego punktu końcowego

![przegląd][10]
![końcowym widoku prywatnego punktu końcowego][11]

## <a name="connect-to-a-vm-from-the-internet"></a>Nawiązywanie połączenia z maszyną wirtualną z Internetu

1. Na pasku wyszukiwania portalu wprowadź **myVm**
1. Wybierz **przycisk Połącz**. Po wybraniu przycisku Połącz Nawiąż połączenie z maszyną wirtualną, a następnie wybierz pozycję **RDP** .

![Przycisk RDP][12]

1. Platforma Azure tworzy plik Remote Desktop Protocol (RDP) i pobiera go na komputer po kliknięciu przycisku **Pobierz plik RDP** .

![Pobierz plik RDP][13]

1. Otwórz pobrany plik RDP.

- Jeśli zostanie wyświetlony monit, wybierz pozycję Połącz.
- Wprowadź nazwę użytkownika i hasło określone podczas tworzenia maszyny wirtualnej.

> [!Note]
> Może być konieczne wybranie pozycji więcej opcji, > użyć innego konta, aby określić poświadczenia wprowadzone podczas tworzenia maszyny wirtualnej.

- Wybierz przycisk OK.

1. Podczas procesu logowania może pojawić się ostrzeżenie o certyfikacie. Jeśli zostanie wyświetlone ostrzeżenie dotyczące certyfikatu, wybierz pozycję tak lub Kontynuuj.

1. Po wyświetleniu pulpitu maszyny wirtualnej zminimalizuj ją i wróć z powrotem do pulpitu lokalnego.

## <a name="access-web-app-privately-from-the-vm"></a>Dostęp do aplikacji internetowej prywatnie z poziomu maszyny wirtualnej

W tej sekcji połączysz się prywatnie z aplikacją internetową przy użyciu prywatnego punktu końcowego.

1. Uzyskaj prywatny adres IP prywatnego punktu końcowego, na pasku wyszukiwania wpisz **prywatny link**i wybierz opcję link prywatny.

![Link prywatny][14]

1. W centrum linku prywatnego wybierz **prywatne punkty końcowe** , aby wyświetlić listę wszystkich prywatnych punktów końcowych

![Prywatne centrum linków][15]

1. Wybierz łącze prywatnego punktu końcowego do aplikacji sieci Web i podsieci

![Właściwości prywatnego punktu końcowego][16]

1. Skopiuj prywatny adres IP prywatnego punktu końcowego i nazwę FQDN aplikacji sieci Web, w naszym przypadku webappdemope.azurewebsites.net 10.10.2.4

1. W myVM upewnij się, że aplikacja sieci Web nie jest dostępna za pomocą publicznego adresu IP. Otwórz przeglądarkę i skopiuj nazwę aplikacji sieci Web, musisz mieć niedostępną stronę błędu o 403

![Forbidden][17]

> [!Note]
> Ponieważ ta funkcja jest dostępna w wersji zapoznawczej, należy ręcznie zarządzać wpisem DNS.

1. Utwórz wpis hosta, otwórz Eksploratora plików i zlokalizuj plik hosts

![Plik hosts][18]

1. Dodaj wpis z prywatnym adresem IP i publiczną nazwą aplikacji sieci Web, edytując plik hosts za pomocą programu Notepad

![Zawartość hosta][19]

1. Zapisz plik.

1. Otwórz przeglądarkę i wpisz adres URL aplikacji sieci Web

![Witryna sieci Web z PE][20]

1. Uzyskujesz dostęp do aplikacji sieci Web za pomocą prywatnego punktu końcowego

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy skończysz korzystać z prywatnego punktu końcowego, aplikacji sieci Web i maszyny wirtualnej, Usuń grupę zasobów i wszystkie zawarte w niej zasoby:

1. W polu wyszukiwania w górnej części portalu wprowadź wartość readed-RG, a następnie w wynikach wyszukiwania wybierz pozycję gotowe-RG.
1. Wybierz pozycję Usuń grupę zasobów.
1. Wprowadź wartość Read-RG, aby wpisać nazwę grupy zasobów, a następnie wybierz pozycję Usuń.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start utworzono MASZYNę wirtualną w sieci wirtualnej, aplikacji sieci Web i prywatnym punkcie końcowym. Nawiązano połączenie z maszyną wirtualną z Internetu i bezpiecznie komunikuje się z aplikacją internetową przy użyciu linku prywatnego. Aby dowiedzieć się więcej o prywatnym punkcie końcowym, zobacz [co to jest prywatny punkt końcowy platformy Azure][privateendpoint].

<!--Image references-->
[1]: ./media/create-private-endpoint-webapp-portal/createnetwork.png
[2]: ./media/create-private-endpoint-webapp-portal/ipaddresses.png
[3]: ./media/create-private-endpoint-webapp-portal/subnet.png
[4]: ./media/create-private-endpoint-webapp-portal/virtualmachine.png
[5]: ./media/create-private-endpoint-webapp-portal/vmnetwork.png
[6]: ./media/create-private-endpoint-webapp-portal/webapp.png
[7]: ./media/create-private-endpoint-webapp-portal/webappnetworking.png
[8]: ./media/create-private-endpoint-webapp-portal/webapppe.png
[9]: ./media/create-private-endpoint-webapp-portal/webapppenetwork.png
[10]: ./media/create-private-endpoint-webapp-portal/inprogress.png
[11]: ./media/create-private-endpoint-webapp-portal/webapppefinal.png
[12]: ./media/create-private-endpoint-webapp-portal/rdp.png
[13]: ./media/create-private-endpoint-webapp-portal/rdpdownload.png
[14]: ./media/create-private-endpoint-webapp-portal/pl.png
[15]: ./media/create-private-endpoint-webapp-portal/plcenter.png
[16]: ./media/create-private-endpoint-webapp-portal/privateendpointproperties.png
[17]: ./media/create-private-endpoint-webapp-portal/forbidden.png
[18]: ./media/create-private-endpoint-webapp-portal/explorer.png
[19]: ./media/create-private-endpoint-webapp-portal/hosts.png
[20]: ./media/create-private-endpoint-webapp-portal/webappwithpe.png

<!--Links-->
[privateendpoint]: https://docs.microsoft.com/azure/private-link/private-endpoint-overview
