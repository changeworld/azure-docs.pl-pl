---
title: Łączenie się prywatnie z aplikacją sieci Web przy użyciu prywatnego punktu końcowego platformy Azure
description: Łączenie się prywatnie z aplikacją sieci Web przy użyciu prywatnego punktu końcowego platformy Azure
author: ericgre
ms.assetid: b8c5c7f8-5e90-440e-bc50-38c990ca9f14
ms.topic: article
ms.date: 03/12/2020
ms.author: ericg
ms.service: app-service
ms.workload: web
ms.openlocfilehash: 2f10c7378ae7681b14df6e96b6a6f1adac832d1b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80287819"
---
# <a name="connect-privately-to-a-web-app-using-azure-private-endpoint-preview"></a>Łączenie się prywatnie z aplikacją sieci Web przy użyciu prywatnego punktu końcowego platformy Azure (wersja zapoznawcza)

Prywatny punkt końcowy platformy Azure jest podstawowym blokiem konstrukcyjnym dla łącza prywatnego na platformie Azure. Umożliwia prywatne łączenie się z aplikacją sieci Web.
W tym przewodniku Szybki start dowiesz się, jak wdrożyć aplikację sieci Web z prywatnym punktem końcowym i połączyć się z tą aplikacją sieci Web z maszyny wirtualnej.

Aby uzyskać więcej informacji, zobacz [Korzystanie z prywatnych punktów końcowych dla aplikacji Azure Web App][privatenedpointwebapp].

> [!Note]
>Wersja zapoznawcza jest dostępna w regionach wschodnich stanów USA i zachodnie stany USA 2 dla wszystkich aplikacji PremiumV2 Windows i Linux Web Apps. 

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do witryny Azure Portal pod adresem https://portal.azure.com.

## <a name="virtual-network-and-virtual-machine"></a>Sieć wirtualna i maszyna wirtualna

W tej sekcji utworzysz sieć wirtualną i podsieć do obsługi maszyny Wirtualnej, która jest używana do uzyskiwania dostępu do aplikacji sieci Web za pośrednictwem prywatnego punktu końcowego.

### <a name="create-the-virtual-network"></a>Tworzenie sieci wirtualnej

W tej sekcji utworzysz sieć wirtualną i podsieć.

1. W lewym górnym rogu ekranu wybierz pozycję **Utwórz zasób** > **Sieć** > **wirtualna** sieci lub wyszukaj w polu wyszukiwania **sieć wirtualną.**

1. W **obszarze Tworzenie sieci wirtualnej**wprowadź lub wybierz te informacje na karcie Podstawy:

   > [!div class="mx-imgBorder"]
   > ![Tworzenie sieci wirtualnej][1]

1. Kliknij **"Dalej: adresy IP >"** i wprowadź lub wybierz te informacje:

   > [!div class="mx-imgBorder"]
   >![Konfigurowanie adresów IP][2]

1. W sekcji podsieci kliknij **"+ Dodaj podsieć"** i wprowadź następujące informacje i kliknij **"Dodaj"**

   > [!div class="mx-imgBorder"]
   >![Dodaj podsieć][3]

1. Kliknij **"Recenzja + utwórz"**

1. Po zakończeniu sprawdzania poprawności kliknij przycisk **"Utwórz"**

### <a name="create-virtual-machine"></a>Tworzenie maszyny wirtualnej

1. W lewym górnym rogu ekranu w portalu Azure wybierz pozycję Utwórz**maszynę wirtualną** **obliczeń** >  **zasobów** > 

1. W obszarze Tworzenie maszyny wirtualnej — ustawienia podstawowe wprowadź lub wybierz następujące informacje:

   > [!div class="mx-imgBorder"]
   >![Maszyna wirtualna podstawowa][4]

1. Wybierz **"Dalej: Dyski"**

   Zachowaj ustawienia domyślne.

1. Wybierz **"Dalej: Sieć"**, wybierz te informacje:

   > [!div class="mx-imgBorder"]
   >![Sieć][5]

1. Kliknij **"Recenzja + Utwórz"**

1. Gdy wiadomość o weryfikacji przebiegła, kliknij przycisk **"Utwórz"**

## <a name="create-your-web-app-and-private-endpoint"></a>Tworzenie aplikacji sieci Web i prywatnego punktu końcowego

W tej sekcji utworzysz prywatną aplikację sieci Web przy użyciu prywatnego punktu końcowego do niego.

> [!Note]
>Funkcja Prywatnego punktu końcowego jest dostępna tylko dla jednostki SKU Premium V2.

### <a name="web-app"></a>Aplikacja internetowa

1. W lewym górnym rogu ekranu w portalu Azure wybierz pozycję **Utwórz zasób** > **aplikacji sieci** > **Web**

1. W obszarze Tworzenie aplikacji sieci Web — podstawy wprowadź lub wybierz te informacje:

   > [!div class="mx-imgBorder"]
   >![Podstawowa aplikacja sieci Web][6]

1. Wybierz **"Recenzja + utwórz"**

1. Gdy wiadomość o weryfikacji przebiegła, kliknij przycisk **"Utwórz"**

### <a name="create-the-private-endpoint"></a>Tworzenie prywatnego punktu końcowego

1. We właściwościach aplikacji sieci Web wybierz **pozycję Ustawienia** > **sieci i** kliknij **"Skonfiguruj połączenia prywatnego punktu końcowego"**

   > [!div class="mx-imgBorder"]
   >![Sieci aplikacji sieci Web][7]

1. W kreatorze kliknij **"+ dodaj"**

   > [!div class="mx-imgBorder"]
   >![Prywatny punkt końcowy aplikacji sieci Web][8]

1. Wypełnij informacje o subskrypcji, sieci wirtualnej i podsieci i kliknij przycisk **"OK"**

   > [!div class="mx-imgBorder"]
   >![Tworzenie sieci aplikacji sieci Web][9]

1. Przegląd tworzenia prywatnego punktu końcowego

   > [!div class="mx-imgBorder"]
   >![Przegląd][10]
   >![Widok końcowy prywatnego punktu końcowego][11]

## <a name="connect-to-a-vm-from-the-internet"></a>Nawiązywanie połączenia z maszyną wirtualną z Internetu

1. Na pasku wyszukiwania portalu wpisz **myVm**
1. Wybierz **przycisk Połącz**. Po wybraniu przycisku Połącz, otworzy się opcję Połącz z maszyną wirtualną, wybierz **pozycję RDP**

   > [!div class="mx-imgBorder"]
   >![Przycisk RDP][12]

1. Platforma Azure tworzy plik protokołu rdp (Remote Desktop Protocol) i pobiera go na komputer po kliknięciu **pliku Pobierz plik RDP**

   > [!div class="mx-imgBorder"]
   >![Pobierz plik RDP][13]

1. Otwórz pobrany plik rdp.

- Po wyświetleniu monitu wybierz pozycję Połącz.
- Wprowadź nazwę użytkownika i hasło określone podczas tworzenia maszyny Wirtualnej.

> [!Note]
> Może być konieczne wybranie opcji Więcej opcji > Użyj innego konta, aby określić poświadczenia wprowadzone podczas tworzenia maszyny Wirtualnej.

- Wybierz przycisk OK.

1. Podczas procesu logowania może pojawić się ostrzeżenie o certyfikacie. Jeśli zostanie wyświetlone ostrzeżenie o certyfikacie, wybierz opcję Tak lub Kontynuuj.

1. Po wyświetleniu pulpitu maszyny wirtualnej zminimalizuj ją i wróć z powrotem do pulpitu lokalnego.

## <a name="access-web-app-privately-from-the-vm"></a>Prywatnie uzyskiwanie dostępu do aplikacji Sieci Web z maszyny Wirtualnej

W tej sekcji połączysz się prywatnie z aplikacją sieci Web przy użyciu prywatnego punktu końcowego.

1. Pobierz prywatny adres IP prywatnego punktu końcowego, w pasku wyszukiwania wpisz **Private Link**i wybierz pozycję Łącze prywatne

   > [!div class="mx-imgBorder"]
   >![Link prywatny][14]

1. W Centrum łączy prywatnych wybierz **pozycję Prywatne punkty końcowe,** aby wyświetlić listę wszystkich prywatnych punktów końcowych

   > [!div class="mx-imgBorder"]
   >![Prywatne centrum łączy][15]

1. Wybieranie łącza Prywatny punkt końcowy do aplikacji sieci Web i podsieci

   > [!div class="mx-imgBorder"]
   >![Właściwości prywatnego punktu końcowego][16]

1. Skopiuj prywatny adres IP prywatnego punktu końcowego i numer FQDN aplikacji sieci Web, w naszym przypadku webappdemope.azurewebsites.net 10.10.2.4

1. W myVM sprawdź, czy aplikacja sieci Web nie jest dostępna za pośrednictwem publicznego adresu IP. Otwórz przeglądarkę i wklej nazwę aplikacji sieci Web, musisz mieć stronę zakazanego błędu 403

   > [!div class="mx-imgBorder"]
   >![Forbidden][17]

> [!Important]
> Ponieważ ta funkcja jest w wersji zapoznawczej, należy ręcznie zarządzać wpisem DNS.

1. Tworzenie wpisu hosta, otwieranie eksploratora plików i lokalizowanie pliku hostów

   > [!div class="mx-imgBorder"]
   >![Plik Hosts][18]

1. Dodawanie wpisu z prywatnym adresem IP i nazwą publiczną aplikacji sieci Web przez edycję pliku hosts za pomocą notatnika

   > [!div class="mx-imgBorder"]
   >![Hostuje zawartość][19]

1. Zapisz plik.

1. Otwórz przeglądarkę i wpisz adres URL aplikacji internetowej

   > [!div class="mx-imgBorder"]
   >![Witryna sieci Web z pe][20]

1. Uzyskujesz dostęp do aplikacji sieci Web za pośrednictwem prywatnego punktu końcowego

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Po zakończeniu korzystania z prywatnego punktu końcowego, aplikacji sieci Web i maszyny Wirtualnej usuń grupę zasobów i wszystkie zasoby, które zawiera:

1. Wpisz ready-rg w polu wyszukiwania u góry portalu i wybierz ready-rg z wyników wyszukiwania.
1. Wybierz pozycję Usuń grupę zasobów.
1. Wprowadź ready-rg dla TYPU NAZWA GRUPY ZASOBÓW i wybierz pozycję Usuń.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start utworzono maszynę wirtualną w sieci wirtualnej, aplikacji sieci Web i prywatnym punkcie końcowym. Połączenie z maszyną wirtualną z Internetu i bezpieczne komunikowanie się z aplikacją sieci Web przy użyciu łącza prywatnego. Aby dowiedzieć się więcej o prywatnym punkcie końcowym, zobacz [Co to jest prywatny punkt końcowy platformy Azure][privateendpoint].

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
[privatenedpointwebapp]: https://docs.microsoft.com/azure/app-service/networking/private-endpoint
[privateendpoint]: https://docs.microsoft.com/azure/private-link/private-endpoint-overview
