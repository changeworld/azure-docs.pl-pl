---
title: Rozwiązanie Azure VMware firmy CloudSimple — konfigurowanie vCenter w chmurze prywatnej dla automatyzacji vRealize
description: W tym artykule opisano sposób konfigurowania serwera vCenter VMware w chmurze CloudSimple Private Cloud jako punktu końcowego automatyzacji VMware vRealize
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/19/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: df73acfc469a8b7b5329b61095aefdbd73baafd4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77024844"
---
# <a name="set-up-vcenter-on-your-private-cloud-for-vmware-vrealize-automation"></a>Konfigurowanie vCenter w chmurze prywatnej dla automatyzacji VMware vRealize

Serwer VMware vCenter można skonfigurować w chmurze CloudSimple Private Cloud jako punkt końcowy dla automatyzacji VMware vRealize.

## <a name="before-you-begin"></a>Przed rozpoczęciem

Wykonaj następujące zadania przed skonfigurowaniem serwera vCenter:

* Skonfiguruj [połączenie sieci VPN lokacja lokacja](vpn-gateway.md#set-up-a-site-to-site-vpn-gateway) między środowiskiem lokalnym a chmurą prywatną.
* [Skonfiguruj przekazywanie lokalnych żądań DNS do serwerów](on-premises-dns-setup.md) DNS w chmurze prywatnej.
* Prześlij [żądanie pomocy technicznej,](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) aby utworzyć użytkownika administracyjnego usługi VRealize Automation IaaS z zestawem uprawnień wymienionych w poniższej tabeli.

| Wartość atrybutu | Uprawnienie |
------------ | ------------- |  
| Magazyn danych |  Przydzielanie miejsca <br> Przeglądaj magazyn danych |
| Klaster magazynu danych | Konfigurowanie klastra magazynu danych |
| Folder | Tworzenie folderu <br>Usuń folder |
| Globalny |  Zarządzanie atrybutami niestandardowymi<br>Ustawianie atrybutu niestandardowego |
| Network (Sieć) | Przypisywanie sieci |
| Uprawnienia | Modyfikowanie uprawnień |
| Zasób | Przypisywanie maszyny Wirtualnej do puli zasobów<br>Migrowanie wyłączonej maszyny wirtualnej<br>Migrowanie na maszynie wirtualnej |
| Spis maszyn wirtualnych |  Tworzenie na podstawie istniejących<br>Kliknięcie przycisku Nowe<br>Move<br>Remove | 
| Interakcja z maszyną wirtualną |  Konfigurowanie nośnika CD<br>Interakcja z konsolą<br>Połączenie urządzenia<br>Wyłącz zasilanie<br>Włączanie zasilania<br>Reset<br>Wstrzymanie<br>Instalacja narzędzi | 
| Konfiguracja maszyny wirtualnej |  Dodawanie istniejącego dysku<br>Dodaj nowy dysk<br>Dodawanie lub usuwanie<br>Usuń dysk<br>Zaawansowane<br>Zmienianie liczby procesorów<br>Zmienianie zasobu<br>Rozszerzanie dysku wirtualnego<br>Śledzenie zmian dysku<br>Memory (Pamięć)<br>Modyfikowanie ustawień urządzenia<br>Zmiana nazwy<br>Ustaw adnotację (wersja 5.0 lub nowsza)<br>Ustawienia<br>Umieszczenie pliku wymiany |
| Inicjowanie obsługi |  Dostosowywanie<br>Szablon klonowania<br>Klonuj maszynę wirtualną<br>Wdrażanie szablonu<br>Przeczytaj specyfikacje dostosowywania |
| Stan maszyny wirtualnej | Utwórz migawkę<br>Usuń migawkę<br>Powrót do migawki |

## <a name="install-vrealize-automation-in-your-on-premises-environment"></a>Instalowanie automatyzacji vRealize w środowisku lokalnym

1. Zaloguj się do urządzenia serwera VRealize Automation IaaS jako administrator IaaS utworzony dla Ciebie przez pomoc techniczną CloudSimple.
2. Wdrażanie agenta vSphere dla punktu końcowego automatyzacji vRealize.
    1. Przejdź do https:// vra-url:5480/installer, gdzie *vra-url* jest adresem URL używanym do*uzyskiwania*dostępu do interfejsu użytkownika administracji vRealize Automation.
    2. Kliknij **instalator IaaS,** aby pobrać instalator.<br>
    Konwencja nazewnictwa pliku instalatora jest setup_*vra-url*@5480.exe.
    3. Uruchom instalatora. Na ekranie Zapraszamy naciśnij przycisk **Dalej**.
    4. Zaakceptuj eula i kliknij przycisk **Dalej**.
    5. Podaj informacje logowania, kliknij przycisk **Zaakceptuj certyfikat,** a następnie kliknij przycisk **Dalej**.
    ![Poświadczenia vRA](media/configure-vra-endpoint-login.png)
    6. Wybierz **pozycję Instalacja niestandardowa** i **Agenci proxy** i kliknij przycisk **Dalej**.
    ![Typ instalacji vRA](media/configure-vra-endpoint-install-type.png)
    7. Wprowadź informacje logowania serwera IaaS i kliknij przycisk **Dalej**. Jeśli używasz usługi Active Directory, wprowadź nazwę użytkownika w formacie **domeny\użytkownik.** W przeciwnym **user@domain** razie użyj formatu.
    ![Informacje logowania vRA](media/configure-vra-endpoint-account.png)
    8. W przypadku ustawień serwera proxy wprowadź **vSphere** dla **typu agenta**. Wprowadź nazwę agenta.
    9. Wprowadź numer FQDN serwera IaaS w polach **Host usługi menedżera** i **hosta usługi sieci Web Menedżera modelu.** Kliknij **przycisk Testuj,** aby przetestować połączenie dla każdej z wartości FQDN. Jeśli test nie powiedzie się, zmodyfikuj ustawienia DNS, tak aby nazwa hosta serwera IaaS została rozwiązana.
    10. Wprowadź nazwę punktu końcowego serwera vCenter dla chmury prywatnej. Zapisz nazwę do użycia w dalszej części procesu konfiguracji.

        ![serwer proxy instalacji vRA](media/configure-vra-endpoint-proxy.png)

    11. Kliknij przycisk **alej**.
    12. Kliknij **pozycję Zainstaluj**.

## <a name="configure-the-vsphere-agent"></a>Konfigurowanie agenta vSphere

1. Przejdź do https://*vra-url*/vcac i zaloguj się jako **ConfigurationAdmin**.
2. Wybierz **punkty** > **końcowe** > **Endpoints**infrastruktury .
3. Wybierz **nowy** > **wirtualny** > **vSphere**.
4. Wprowadź nazwę punktu końcowego vSphere określoną w poprzedniej procedurze.
5. W przypadku **adresu**wprowadź adres URL serwera vCenter Private Cloud w formacie https://*vcenter-fqdn*/sdk, gdzie *vcenter-fqdn* jest nazwą serwera vCenter.
6. Wprowadź poświadczenia dla użytkownika administracyjnego usługi VRealize Automation IaaS utworzonego dla użytkownika usługi CloudSimple.
7. Kliknij **przycisk Testuj połączenie,** aby sprawdzić poprawność poświadczeń użytkownika. Jeśli test zakończy się niepowodzeniem, sprawdź adres URL, informacje o koncie i [nazwę punktu końcowego](#verify-the-endpoint-name) i przetestuj ponownie.
8. Po pomyślnym teście kliknij przycisk **OK,** aby utworzyć punkt końcowy vSphere.
    ![dostęp do konfiguracji punktu końcowego vRA](media/configure-vra-endpoint-vra-edit.png)

### <a name="verify-the-endpoint-name"></a>Weryfikowanie nazwy punktu końcowego

Aby zidentyfikować prawidłową nazwę punktu końcowego serwera vCenter, wykonaj następujące czynności:

1. Otwórz wiersz polecenia na urządzeniu IaaS.
2. Zmień katalog na C:\Program Files (x86)\VMware\vCAC\Agents\agent-name, gdzie *nazwa agenta* to nazwa przypisana do punktu końcowego serwera vCenter.
3. Uruchom następujące polecenie.

```
..\..\Server\DynamicOps.Vrm.VRMencrypt.exe VRMAgent.exe.config get
```

Dane wyjściowe są podobne do następujących. Wartość `managementEndpointName` pola jest nazwą punktu końcowego.

```
managementEndpointName: cslab1pc3-vc
doDeletes: true
```
