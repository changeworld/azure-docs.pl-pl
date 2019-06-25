---
title: Samouczek dotyczący tworzenia infrastruktury dla usługi Service Fabric klastra na maszynach wirtualnych platformy Azure — usłudze Azure Service Fabric | Dokumentacja firmy Microsoft
description: W tym samouczku dowiesz się, jak skonfigurować infrastrukturę maszyny Wirtualnej platformy Azure do korzystania z klastra usługi Service Fabric.
services: service-fabric
documentationcenter: .net
author: v-vasuke
manager: jpconnock
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 3/25/2019
ms.author: v-vasuke
ms.custom: mvc
ms.openlocfilehash: b5f2f77b3caed483aed1736bd510096d44329284
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/20/2019
ms.locfileid: "67276006"
---
# <a name="tutorial-create-azure-vm-infrastructure-to-host-a-service-fabric-cluster"></a>Samouczek: Tworzenie infrastruktury maszyny Wirtualnej platformy Azure do hostowania klastra usługi Service Fabric

Klastry autonomiczne usługi Service Fabric umożliwiają wybór własnego środowiska i utworzenie klastra zgodnie z obowiązującą w usłudze Service Fabric zasadą „dowolnego systemu operacyjnego i dowolnej chmury”. W tej serii samouczków, tworzenie klastra autonomicznego hostowanych na maszynach wirtualnych platformy Azure i zainstalować aplikację na niego.

Niniejszy samouczek jest pierwszą częścią serii. W tym artykule musisz wygenerować zasoby maszyny Wirtualnej platformy Azure wymagane do obsługi klastra autonomicznego usługi Service Fabric. W następnych artykułach opisano sposób instalowania autonomicznego zestawu usługi Service Fabric, instalowania w klastrze przykładowej aplikacji i na koniec czyszczenia klastra.

Część pierwsza serii zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Utwórz zestaw wystąpień AzureVM
> * Modyfikowanie grupy zabezpieczeń
> * Logowanie się do jednego z wystąpień
> * Przygotowywanie wystąpienia na potrzeby usługi Service Fabric

## <a name="prerequisites"></a>Wymagania wstępne

Do wykonania kroków tego samouczka potrzebna jest subskrypcja platformy Azure.  Jeśli nie masz jeszcze konta, przejdź do strony [witryny Azure portal](https://portal.azure.com) ją utworzyć.

## <a name="create-azure-virtual-machine-instances"></a>Tworzenie wystąpień maszyny wirtualnej platformy Azure

1. Zaloguj się w witrynie Azure portal i wybierz **maszyn wirtualnych** (nie maszyny wirtualne (wersja klasyczna)).

   ![Maszynę Wirtualną witryny Azure portal][az-console]

2. Wybierz **Dodaj** przycisk, który zostanie otwarty **Utwórz maszynę wirtualną** formularza.

3. W **podstawy** kartę, należy wybrać subskrypcję i grupę zasobów (przy użyciu nowej grupy zasobów jest zalecane).

4. Zmiana **obraz** typ **systemu Windows Server 2016 Datacenter**. 
 
5. Zmiana wystąpienia **rozmiar** do **standardowa DS2, wersja 2**. Ustaw administratora **Username** i **hasło**, biorąc pod uwagę, jakie są.

6. Pozostaw **reguły portów wejściowych** zablokowane teraz; zostaną skonfigurowane w następnej sekcji.

7. W **sieć** pozycję Utwórz nowy **sieci wirtualnej** i zwróć uwagę na nazwy.

8. Następnym etapem jest skonfigurowanie **karty Sieciowej sieciowej grupy zabezpieczeń** do **zaawansowane**. Utwórz nową grupę zabezpieczeń, biorąc pod uwagę jej nazwy i Utwórz następujące reguły, aby umożliwić ruch TCP z dowolnego źródła:

   ![Liczba przychodzących SF][sf-inbound]

   * Port `3389`dla protokołu RDP i protokołu ICMP (podstawowej łączności).
   * Porty `19000-19003`, dla usługi Service Fabric.
   * Porty `19080-19081`, dla usługi Service Fabric.
   * Port `8080`, żądań przeglądarki sieci web.

   > [!TIP]
   > Aby połączyć maszyny wirtualne z usługą Service Fabric, maszyny wirtualne, które hostują infrastrukturę, muszą mieć takie same poświadczenia.  Istnieją dwa podstawowe sposoby na uzyskanie spójnych poświadczeń: dołączenie wszystkich hostów do tej samej domeny lub ustawienie takiego samego hasła administratora na każdej maszynie wirtualnej. Na szczęście, platforma Azure zezwoli wszystkie maszyny wirtualne na tym samym **sieć wirtualna** łatwe łączenie, dzięki czemu firma Microsoft ponosi upewnij się, że wszystkie nasze wystąpienia w tej samej sieci.

9. Dodaj inną regułę. Ustaw źródło jako **Tag usługi** i ustaw tag usługi źródłowej **VirtualNetwork**. Usługa Service Fabric wymaga następujących portów do komunikacji w klastrze: 135,137-139,445,20001-20031,20606-20861.

   ![vnet-inbound][vnet-inbound]

10. Pozostałe opcje są akceptowane w ich stanu domyślnego. Jeśli chcesz je przejrzeć, a następnie uruchom maszynę wirtualną.

## <a name="creating-more-instances-for-your-service-fabric-cluster"></a>Tworzenie większej liczby wystąpień dla klastra usługi Service Fabric

Uruchamianie dwóch **maszyn wirtualnych**, upewniając się, że obsługa tych samych ustawień, które są opisane w poprzedniej sekcji. Obsługa szczególnie, nazwę użytkownika administratora i hasła. **Sieci wirtualnej** i **karty Sieciowej sieciowej grupy zabezpieczeń** nie powinny być odtwarzane; wybrać te, utworzone z menu rozwijanego. Może upłynąć kilka minut dla poszczególnych wystąpień do wdrożenia.

## <a name="connect-to-your-instances"></a>Łączenie do wystąpień

1. Wybierz jedno z wystąpień z **maszyny wirtualnej** sekcji.

2. W **Przegląd** kartę, zwróć uwagę na *prywatnej* adresu IP. Następnie kliknij przycisk **Connect**.

3. W **RDP** kartę, należy pamiętać, że używasz publicznego adresu IP i portu 3389, które w szczególności otwarcia wcześniej. Pobierz plik RDP.
 
4. Otwórz plik RDP, a po wyświetleniu monitu wprowadź nazwę użytkownika i hasło podane w ustawieniach maszyny Wirtualnej.

5. Po nawiązaniu połączenia z wystąpieniem należy sprawdzić, czy rejestr zdalny była uruchomiona, Włącz protokół SMB i otworzyć wymagane porty dla protokołu SMB i Rejestr zdalny.

   Aby włączyć protokół SMB, to polecenie programu PowerShell:

   ```powershell
   netsh advfirewall firewall set rule group="File and Printer Sharing" new enable=Yes
   ```

6. W celu otwarcia portów w zaporze użyto tego polecenia programu PowerShell:

   ```powershell
   New-NetFirewallRule -DisplayName "Service Fabric Ports" -Direction Inbound -Action Allow -RemoteAddress LocalSubnet -Protocol TCP -LocalPort 135, 137-139, 445
   ```

7. Powtórz ten proces dla innych wystąpień, biorąc pod uwagę prywatnych adresów IP, ponownie.

## <a name="verify-your-settings"></a>Sprawdź ustawienia

1. Aby zweryfikować połączenie podstawowe, połącz się z jednym z maszyn wirtualnych przy użyciu protokołu RDP.

2. Otwórz **polecenia** z tej maszynie wirtualnej, a następnie użyj polecenia ping nawiązać połączenie z jedną maszynę Wirtualną do innego, zastępując poniżej adres IP przy użyciu jednego z prywatnym adresem IP adresów zanotowanej wcześniej (nie adres IP maszyny wirtualnej, w którym nawiązano połączenie już).

   ```
   ping 172.31.20.163
   ```

   Jeśli w Twoich danych wyjściowych cztery razy pojawia się komunikat podobny do tego: `Reply from 172.31.20.163: bytes=32 time<1ms TTL=128`, oznacza to, że połączenie między wystąpieniami działa.

3. Teraz użyj następującego polecenia, aby zweryfikować, że udostępnianie za pomocą protokołu SMB działa:

   ```
   net use * \\172.31.20.163\c$
   ```

   Powinien zostać wyświetlony komunikat podobny do następującego: `Drive Z: is now connected to \\172.31.20.163\c$.`.


   Teraz wystąpień są poprawnie przygotowany dla usługi Service Fabric.

## <a name="next-steps"></a>Kolejne kroki

W części pierwszej serii przedstawiono sposób uruchamiania trzy wystąpienia maszyn wirtualnych platformy Azure i ich skonfigurowane podczas instalacji usługi Service Fabric:

> [!div class="checklist"]
> * Utwórz zestaw wystąpień maszyny Wirtualnej platformy Azure
> * Modyfikowanie grupy zabezpieczeń
> * Logowanie się do jednego z wystąpień
> * Przygotowywanie wystąpienia na potrzeby usługi Service Fabric

Przejdź do części drugiej tej serii, aby skonfigurować usługę Service Fabric w klastrze.

> [!div class="nextstepaction"]
> [Zainstaluj usługę Service Fabric](service-fabric-tutorial-standalone-create-service-fabric-cluster.md)

<!-- IMAGES -->
[az-console]: ./media/service-fabric-tutorial-standalone-azure-create-infrastructure/az-console.png
[sf-inbound]: ./media/service-fabric-tutorial-standalone-azure-create-infrastructure/sf-inbound.png
[vnet-inbound]: ./media/service-fabric-tutorial-standalone-azure-create-infrastructure/vnet-inbound.png
