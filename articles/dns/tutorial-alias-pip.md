---
title: Samouczek — tworzenie rekordu aliasu usługi Azure DNS w celu odwoływania się do publicznego adresu IP platformy Azure.
description: W tym samouczku pokazano, jak skonfigurować rekord aliasu usługi Azure DNS w celu odwoływania się do publicznego adresu IP platformy Azure.
services: dns
author: vhorne
ms.service: dns
ms.topic: tutorial
ms.date: 9/25/2018
ms.author: victorh
ms.openlocfilehash: 842015d853790e3ac78214cca6a70becb7f9fadf
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "46991233"
---
# <a name="tutorial-configure-an-alias-record-to-refer-to-an-azure-public-ip-address"></a>Samouczek: konfigurowanie rekordu aliasu w celu odwoływania się do publicznego adresu IP platformy Azure 

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie infrastruktury sieci
> * Tworzenie maszyny wirtualnej serwera sieci Web
> * Tworzenie rekordu aliasu
> * Testowanie rekordu aliasu


Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Wymagania wstępne
Do testowania niezbędna jest nazwa domeny, którą można hostować w usłudze Azure DNS. Musisz mieć pełną kontrolę nad tą domeną, w tym możliwość ustawiania dla domeny rekordów serwera nazw (NS).

Aby uzyskać instrukcje dotyczące hostowania własnej domeny w usłudze Azure DNS, zobacz [Samouczek: hostowanie własnej domeny w usłudze Azure DNS](dns-delegate-domain-azure-dns.md).

Przykładowa domena używana w tym samouczku to contoso.com, ale należy korzystać z własnej nazwy domeny.

## <a name="create-the-network-infrastructure"></a>Tworzenie infrastruktury sieci
Najpierw utwórz sieć wirtualną i podsieć, aby umieścić w nich serwery sieci Web.
1. Zaloguj się do witryny Azure Portal pod adresem http://portal.azure.com.
2. W lewym górnym rogu portalu kliknij pozycję **Utwórz zasób**, wpisz *grupa zasobów* w polu wyszukiwania, a następnie utwórz grupę zasobów o nazwie **RG-DNS-Alias-pip**.
3. Kliknij pozycję **Utwórz zasób**, kliknij pozycję **Sieć**, a następnie kliknij pozycję **Sieć wirtualna**.
4. Utwórz sieć wirtualną o nazwie **VNet-Server**, umieść ją w grupie zasobów **RG-DNS-Alias-pip** i nadaj podsieci nazwę **SN-Web**.

## <a name="create-a-web-server-virtual-machine"></a>Tworzenie maszyny wirtualnej serwera sieci Web
1. Kliknij pozycję **Utwórz zasób**, a następnie kliknij pozycję **Maszyna wirtualna z systemem Windows Server 2016**.
2. Wpisz nazwę **Web-01** i umieść maszynę wirtualną w grupie zasobów **RG-DNS-Alias-TM**. Wpisz nazwę użytkownika i hasło, a następnie kliknij przycisk **OK**.
3. W obszarze **Rozmiar** wybierz jednostkę SKU z 8 GB pamięci RAM.
4. W obszarze **Ustawienia** wybierz sieć wirtualną **VNet-Servers** i podsieć **SN-Web**. W przypadku publicznych portów wejściowych wybierz pozycje **HTTP**, **HTTPS** i **RDP (3389)**, a następnie kliknij przycisk **OK**.
5. Na stronie podsumowania kliknij pozycję **Utwórz**.

   Wykonanie tej operacji może zająć kilka minut.

### <a name="install-iis"></a>Instalowanie usług IIS

Zainstaluj usługi IIS na maszynie wirtualnej **Web-01**.

1. Połącz się z maszyną wirtualną **Web-01** i zaloguj się.
2. Na pulpicie nawigacyjnym Menedżera serwera kliknij pozycję **Dodaj role i funkcje**.
3. Kliknij przycisk **Dalej** trzy razy i na stronie **Role serwera** wybierz pozycję **Serwer sieci Web (IIS)**.
4. Kliknij pozycję **Dodaj funkcje** i kliknij przycisk **Dalej**.
5. Kliknij przycisk **Dalej** cztery razy, a następnie kliknij przycisk **Zainstaluj**.

   Ukończenie tej operacji potrwa kilka minut.
6. Po zakończeniu instalacji kliknij przycisk **Zamknij**.
7. Otwórz przeglądarkę sieci Web i przejdź do lokalizacji **localhost**, aby sprawdzić, czy zostanie wyświetlona domyślna strona internetowa usług IIS.

## <a name="create-an-alias-record"></a>Tworzenie rekordu aliasu

Utwórz rekord aliasu wskazujący na publiczny adres IP.

1. Kliknij strefę usługi Azure DNS, aby ją otworzyć.
2. Kliknij pozycję **Zestaw rekordów**.
3. W polu tekstowym **Nazwa** wpisz **web01**.
4. Pozostaw pole **Typ** z ustawieniem rekordu **A**.
5. Kliknij pole wyboru **Zestaw rekordów aliasów**.
6. Kliknij pozycję **Wybierz usługę Azure** i wybierz publiczny adres IP **Web-01-ip**.

## <a name="test-the-alias-record"></a>Testowanie rekordu aliasu

1. W grupie zasobów **RG-DNS-Alias-pip** kliknij maszynę wirtualną **Web-01**. Zanotuj publiczny adres IP.
1. Z poziomu przeglądarki sieci Web przejdź do w pełni kwalifikowanej nazwy domeny dla maszyny wirtualnej Web01-01. Na przykład: **web01.contoso.com**. Powinna zostać wyświetlona domyślna strona internetowa usług IIS.
2. Zamknij przeglądarkę sieci Web.
3. Zatrzymaj maszynę wirtualną **Web-01**, a następnie uruchom ją ponownie.
4. Po ponownym uruchomieniu zanotuj nowy publiczny adres IP maszyny wirtualnej.
5. Otwórz nową przeglądarkę i ponownie przejdź do w pełni kwalifikowanej nazwy domeny dla maszyny wirtualnej Web01-01. Na przykład: **web01.contoso.com**.
6. Nadal powinno to się udać, ponieważ użyto rekordu aliasu w celu wskazania publicznego zasobu adresu IP, a nie standardowego rekordu A.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Kiedy grupa zasobów **RG-DNS-Alias-pip** nie jest już potrzebna, możesz ją usunąć, aby usunąć wszystkie zasoby utworzone w tym samouczku.


## <a name="next-steps"></a>Następne kroki

W tym samouczku skonfigurowano rekord aliasu w celu odwoływania się do publicznego adresu IP platformy Azure. Aby dowiedzieć się więcej o usłudze Azure DNS i aplikacjach internetowych, przejdź do samouczka dotyczącego aplikacji internetowych.

> [!div class="nextstepaction"]
> [Create DNS records for a web app in a custom domain (Tworzenie rekordów DNS aplikacji internetowej w domenie niestandardowej)](./dns-web-sites-custom-domain.md)
