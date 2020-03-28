---
title: Tworzenie infrastruktury dla klastra na maszynach wirtualnych platformy Azure
description: W tym samouczku dowiesz się, jak skonfigurować infrastrukturę maszyny wirtualnej platformy Azure do uruchamiania klastra sieci szkieletowej usług.
ms.topic: tutorial
ms.date: 07/22/2019
ms.custom: mvc
ms.openlocfilehash: 93a7e2507ab3a467ef83924479872694cae2dd5b
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "75614013"
---
# <a name="tutorial-create-azure-vm-infrastructure-to-host-a-service-fabric-cluster"></a>Samouczek: tworzenie infrastruktury maszyny Wirtualnej platformy Azure do obsługi klastra sieci szkieletowej usług

Klastry autonomiczne usługi Service Fabric umożliwiają wybór własnego środowiska i utworzenie klastra zgodnie z obowiązującą w usłudze Service Fabric zasadą „dowolnego systemu operacyjnego i dowolnej chmury”. W tej serii samouczków utworzysz autonomiczny klaster hostowany na maszynach wirtualnych platformy Azure i zainstaluj na nim aplikację.

Niniejszy samouczek jest pierwszą częścią serii. W tym artykule wygenerujesz zasoby maszyny Wirtualnej platformy Azure wymagane do hostowania autonomicznego klastra sieci szkieletowej usług. W następnych artykułach opisano sposób instalowania autonomicznego zestawu usługi Service Fabric, instalowania w klastrze przykładowej aplikacji i na koniec czyszczenia klastra.

Część pierwsza serii zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie zestawu wystąpień usługi AzureVM
> * Modyfikowanie grupy zabezpieczeń
> * Logowanie się do jednego z wystąpień
> * Przygotowywanie wystąpienia na potrzeby usługi Service Fabric

## <a name="prerequisites"></a>Wymagania wstępne

Do wykonania kroków tego samouczka potrzebna jest subskrypcja platformy Azure.  Jeśli nie masz jeszcze konta, przejdź do [witryny Azure Portal,](https://portal.azure.com) aby je utworzyć.

## <a name="create-azure-virtual-machine-instances"></a>Tworzenie wystąpień maszyny wirtualnej platformy Azure

1. Zaloguj się do witryny Azure portal i wybierz **maszyny wirtualne** (nie maszyny wirtualne (klasyczne)).

   ![Maszyna wirtualna portalu Azure][az-console]

2. Wybierz przycisk **Dodaj,** który otworzy formularz **Utwórz maszynę wirtualną.**

3. Na karcie **Podstawy** należy wybrać odpowiednią grupę subskrypcji i zasobów (zaleca się użycie nowej grupy zasobów).

4. Zmień typ **obrazu** na **centrum danych systemu Windows Server 2016**. 
 
5. Zmień **rozmiar** wystąpienia na **Standardowy DS2 v2**. Ustaw **nazwę użytkownika** i **hasło administratora**, zwracając się do tego, czym są.

6. Na razie pozostaw zablokowane **reguły portów przychodzących;** skonfigurujemy je w następnej sekcji.

7. Na karcie **Sieć** utwórz nową **sieć wirtualną** i zanotuj jej nazwę.

8. Następnie ustaw **grupę zabezpieczeń sieciowej karty sieciowej** na **Zaawansowane**. Utwórz nową grupę zabezpieczeń, od notując jej nazwę, i utwórz następujące reguły, aby zezwolić na ruch TCP z dowolnego źródła:

   ![sf-przychodzące][sf-inbound]

   * port `3389`, dla RDP i ICMP (łączność podstawowa).
   * Porty `19000-19003`, dla sieci szkieletowej usług.
   * Porty `19080-19081`, dla sieci szkieletowej usług.
   * Port `8080`, dla żądań przeglądarki internetowej.

   > [!TIP]
   > Aby połączyć maszyny wirtualne z usługą Service Fabric, maszyny wirtualne, które hostują infrastrukturę, muszą mieć takie same poświadczenia.  Istnieją dwa podstawowe sposoby na uzyskanie spójnych poświadczeń: dołączenie wszystkich hostów do tej samej domeny lub ustawienie takiego samego hasła administratora na każdej maszynie wirtualnej. Na szczęście platforma Azure umożliwia łatwe łączenie wszystkich maszyn wirtualnych w tej **samej sieci wirtualnej,** więc z pewnością będziemy mieć wszystkie nasze wystąpienia w tej samej sieci.

9. Dodaj kolejną regułę. Ustaw źródło jako **Service Tag** i ustaw tag usługi źródłowej na **VirtualNetwork**. Sieć szkieletowa usług wymaga otwarcia następujących portów do komunikacji w klastrze: 135,137-139,445,20001-20031,20606-20861.

   ![vnet przychodzący][vnet-inbound]

10. Pozostałe opcje są dopuszczalne w ich stanie domyślnym. Przejrzyj je, jeśli chcesz, a następnie uruchom maszynę wirtualną.

## <a name="creating-more-instances-for-your-service-fabric-cluster"></a>Tworzenie większej liczby wystąpień dla klastra sieci szkieletowej usług

Uruchom jeszcze dwie **maszyny wirtualne**, zachowując te same ustawienia opisane w poprzedniej sekcji. W szczególności należy zachować tę samą nazwę użytkownika i hasło administratora. Nie należy odtworzeć grupy zabezpieczeń **sieci wirtualnej** i **sieciowej** karty sieciowej; wybierz te, które zostały już utworzone z menu rozwijanego. Wdrożenie każdego wystąpienia może potrwać kilka minut.

## <a name="connect-to-your-instances"></a>Łączenie się z wystąpieniami

1. Wybierz jedno z wystąpień z sekcji **Maszyna wirtualna.**

2. Na karcie **Przegląd** zanotuj *prywatny* adres IP. Następnie kliknij pozycję **Połącz**.

3. W zakładce **RDP** zwróć uwagę, że używamy publicznego adresu IP i portu 3389, który zostały specjalnie otwarte wcześniej. Pobierz plik RDP.
 
4. Otwórz plik RDP, a po wyświetleniu monitu wprowadź nazwę użytkownika i hasło podane w konfiguracji maszyny Wirtualnej.

5. Po nawiązaniu połączenia z wystąpieniem należy sprawdzić, czy rejestr zdalny był uruchomiony, włączyć SMB i otworzyć wymagane porty dla SMB i rejestru zdalnego.

   Aby włączyć SMB, jest to polecenie programu PowerShell:

   ```powershell
   netsh advfirewall firewall set rule group="File and Printer Sharing" new enable=Yes
   ```

6. W celu otwarcia portów w zaporze użyto tego polecenia programu PowerShell:

   ```powershell
   New-NetFirewallRule -DisplayName "Service Fabric Ports" -Direction Inbound -Action Allow -RemoteAddress LocalSubnet -Protocol TCP -LocalPort 135, 137-139, 445
   ```

7. Powtórz ten proces dla innych wystąpień, ponownie od notując prywatne adresy IP.

## <a name="verify-your-settings"></a>Weryfikowanie ustawień

1. Aby sprawdzić poprawność podstawowej łączności, połącz się z jedną z maszyn wirtualnych przy użyciu protokołu RDP.

2. Otwórz **wiersz polecenia** z poziomu tej maszyny Wirtualnej, a następnie użyj polecenia ping, aby połączyć się z jednej maszyny Wirtualnej do drugiej, zastępując poniższy adres IP jednym z prywatnych adresów IP, które zostały wcześniej odnotowane (nie ip maszyny wirtualnej, z którą jesteś już połączony).

   ```
   ping 172.31.20.163
   ```

   Jeśli w Twoich danych wyjściowych cztery razy pojawia się komunikat podobny do tego: `Reply from 172.31.20.163: bytes=32 time<1ms TTL=128`, oznacza to, że połączenie między wystąpieniami działa.

3. Teraz użyj następującego polecenia, aby zweryfikować, że udostępnianie za pomocą protokołu SMB działa:

   ```
   net use * \\172.31.20.163\c$
   ```

   Powinien zostać wyświetlony komunikat podobny do następującego: `Drive Z: is now connected to \\172.31.20.163\c$.`.


   Teraz wystąpienia są odpowiednio przygotowane do sieci szkieletowej usług.

## <a name="next-steps"></a>Następne kroki

W pierwszej części serii, można dowiedzieć się, jak uruchomić trzy wystąpienia maszyny wirtualnej platformy Azure i uzyskać je skonfigurowane do instalacji sieci szkieletowej usług:

> [!div class="checklist"]
> * Tworzenie zestawu wystąpień maszyn wirtualnych platformy Azure
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
