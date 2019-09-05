---
title: Tworzenie własnego środowiska Integration Runtime w Azure Data Factory | Microsoft Docs
description: Dowiedz się, jak utworzyć własne środowisko Integration Runtime w Azure Data Factory, co umożliwia fabrykom danych dostęp do magazynów danych w sieci prywatnej.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 06/18/2019
author: nabhishek
ms.author: abnarain
manager: craigg
ms.openlocfilehash: be59f5fd34c52397b54146a8aeaf51f4d594452f
ms.sourcegitcommit: 49c4b9c797c09c92632d7cedfec0ac1cf783631b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/05/2019
ms.locfileid: "70383349"
---
# <a name="create-and-configure-a-self-hosted-integration-runtime"></a>Tworzenie i Konfigurowanie własnego środowiska Integration Runtime
Środowisko Integration Runtime (IR) to infrastruktura obliczeniowa, która Azure Data Factory używa do zapewniania możliwości integracji danych w różnych środowiskach sieciowych. Aby uzyskać szczegółowe informacje o IR, zobacz [Omówienie środowiska Integration Runtime](concepts-integration-runtime.md).

Własne środowisko Integration Runtime może uruchamiać działania kopiowania między magazynem danych w chmurze i magazynem danych w sieci prywatnej oraz wysyłać działania transformacji do zasobów obliczeniowych w sieci lokalnej lub w sieci wirtualnej platformy Azure. Instalacja samodzielnego środowiska Integration Runtime na maszynie lokalnej lub maszynie wirtualnej (VM) w sieci prywatnej.  

W tym dokumencie opisano, jak można utworzyć i skonfigurować samoobsługowe środowisko IR.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="high-level-steps-to-install-a-self-hosted-ir"></a>Ogólne kroki instalacji samoobsługowego środowiska IR
1. Utwórz własne środowisko Integration Runtime. Dla tego zadania można użyć interfejsu użytkownika Azure Data Factory. Oto przykład środowiska PowerShell:

    ```powershell
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Name $selfHostedIntegrationRuntimeName -Type SelfHosted -Description "selfhosted IR description"
    ```
  
2. [Pobierz](https://www.microsoft.com/download/details.aspx?id=39717) i zainstaluj własne środowisko Integration Runtime na komputerze lokalnym.

3. Pobierz klucz uwierzytelniania i zarejestruj własne środowisko Integration Runtime przy użyciu klucza. Oto przykład środowiska PowerShell:

    ```powershell

    Get-AzDataFactoryV2IntegrationRuntimeKey -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Name $selfHostedIntegrationRuntimeName  

    ```

## <a name="setting-up-a-self-hosted-ir-on-an-azure-vm-by-using-an-azure-resource-manager-template"></a>Konfigurowanie własnego środowiska IR na maszynie wirtualnej platformy Azure przy użyciu szablonu Azure Resource Manager 
Za pomocą [tego szablonu Azure Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vms-with-selfhost-integration-runtime)można zautomatyzować konfigurację samodzielnego środowiska IR na maszynie wirtualnej platformy Azure. Ten szablon umożliwia łatwe w pełni funkcjonalne środowisko IR w sieci wirtualnej platformy Azure o wysokiej dostępności i skalowalności (o ile liczba węzłów jest ustawiana na 2 lub więcej).

## <a name="command-flow-and-data-flow"></a>Przepływ poleceń i przepływ danych
Gdy przenosisz dane między środowiskiem lokalnym i chmurą, działanie korzysta z własnego środowiska Integration Runtime do transferowania danych z lokalnego źródła danych do chmury i na odwrót.

Poniżej znajduje się ogólny przepływ danych służący do podsumowania kroków kopiowania przy użyciu samodzielnego środowiska IR:

![Ogólne omówienie](media/create-self-hosted-integration-runtime/high-level-overview.png)

1. Deweloper danych tworzy własne środowisko Integration Runtime w ramach fabryki danych platformy Azure przy użyciu polecenia cmdlet programu PowerShell. Obecnie Azure Portal nie obsługuje tej funkcji.
2. Deweloper danych tworzy połączoną usługę dla lokalnego magazynu danych przez określenie wystąpienia środowiska Integration Runtime, które ma być używane do nawiązywania połączeń z magazynami danych.
3. Własny węzeł środowiska Integration Runtime szyfruje poświadczenia za pomocą interfejsu programowania aplikacji ochrony danych systemu Windows (DPAPI) i zapisuje poświadczenia lokalnie. Jeśli skonfigurowano wiele węzłów w celu zapewnienia wysokiej dostępności, poświadczenia są dodatkowo synchronizowane między innymi węzłami. Każdy węzeł szyfruje poświadczenia przy użyciu funkcji DPAPI i przechowuje je lokalnie. Synchronizacja poświadczeń jest niewidoczna dla deweloperów danych i jest obsługiwana przez samoobsługowe środowisko IR.    
4. Usługa Data Factory komunikuje się z własnym hostowanym środowiskiem Integration Runtime na potrzeby planowania i zarządzania zadaniami za pośrednictwem *kanału kontroli* korzystającego z udostępnionego [przekaźnika Azure Service Bus](https://docs.microsoft.com/azure/service-bus-relay/relay-what-is-it#wcf-relay). Gdy zadanie działania musi zostać uruchomione, Data Factory kolejkuje żądanie wraz z dowolnymi informacjami o poświadczeniach (w przypadku poświadczenia nie są już przechowywane w środowisku Integration Runtime). Własne środowisko Integration Runtime uruchamia zadanie po sondowaniu kolejki.
5. Własne środowisko Integration Runtime kopiuje dane z magazynu lokalnego do magazynu w chmurze lub odwrotnie w zależności od sposobu skonfigurowania działania kopiowania w potoku danych. W tym kroku własne środowisko Integration Runtime bezpośrednio komunikuje się z usługami magazynu opartymi na chmurze, takimi jak Azure Blob Storage, za pośrednictwem bezpiecznego kanału (HTTPS).

## <a name="considerations-for-using-a-self-hosted-ir"></a>Zagadnienia dotyczące korzystania z samodzielnego środowiska IR

- Pojedyncze środowisko Integration Runtime może być używane dla wielu lokalnych źródeł danych. Pojedyncze środowisko Integration Runtime może być współużytkowane z inną fabryką danych w ramach tej samej dzierżawy Azure Active Directory. Aby uzyskać więcej informacji, zobacz [udostępnianie własnego środowiska Integration Runtime](#sharing-the-self-hosted-integration-runtime-with-multiple-data-factories).
- Na jednym komputerze można zainstalować tylko jedno wystąpienie środowiska Integration Runtime (samodzielne). Jeśli istnieją dwa fabryki danych, które muszą uzyskać dostęp do lokalnych źródeł danych, użyj [funkcji samodzielnego udostępniania IR](#sharing-the-self-hosted-integration-runtime-with-multiple-data-factories) , aby udostępnić środowisko Integration Runtime (własne), lub zainstalować własne środowisko Integration Runtime na dwóch komputerach lokalnych, jeden dla Każda Fabryka danych.  
- Własne środowisko Integration Runtime nie musi znajdować się na tym samym komputerze co źródło danych. Jednak samodzielne środowisko Integration Runtime bliżej źródła danych skraca czas, w którym środowisko Integration Runtime jest połączone ze źródłem danych. Zalecamy zainstalowanie własnego środowiska Integration Runtime na komputerze innym niż ten, który jest hostem lokalnego źródła danych. Gdy własne środowisko Integration Runtime i źródło danych znajdują się na różnych komputerach, własne środowisko Integration Runtime nie konkuruje o zasoby ze źródłem danych.
- Na różnych komputerach, które łączą się z tym samym lokalnym źródłem danych, można mieć wiele środowisk Integration Runtime. Na przykład mogą istnieć dwa własne środowiska Integration Runtime, które obsługują dwa fabryki danych, ale to samo lokalne źródło danych jest rejestrowane zarówno dla fabryk danych.
- Jeśli na komputerze jest już zainstalowana brama do obsługi scenariusza Power BI, zainstaluj osobne środowisko Integration Runtime dla Azure Data Factory na innym komputerze.
- Własne środowisko Integration Runtime musi być używane do obsługi integracji danych w ramach sieci wirtualnej platformy Azure.
- Traktuj źródło danych jako lokalne źródło danych znajdujące się za zaporą, nawet jeśli korzystasz z usługi Azure ExpressRoute. Użyj własnego środowiska Integration Runtime, aby nawiązać połączenie między usługą a źródłem danych.
- Musisz użyć własnego środowiska Integration Runtime, nawet jeśli magazyn danych znajduje się w chmurze na maszynie wirtualnej IaaS platformy Azure.
- Zadania mogą zakończyć się niepowodzeniem w ramach własnego środowiska Integration Runtime zainstalowanego na serwerze z systemem Windows, na którym włączono szyfrowanie zgodne ze standardem FIPS. Aby obejść ten problem, należy wyłączyć szyfrowanie zgodne ze standardem FIPS na serwerze. Aby wyłączyć szyfrowanie zgodne ze standardem FIPS, należy zmienić następujące wartości rejestru z 1 (włączone) na 0 (wyłączone `HKLM\System\CurrentControlSet\Control\Lsa\FIPSAlgorithmPolicy\Enabled`):.

## <a name="prerequisites"></a>Wymagania wstępne

- Obsługiwane wersje systemu operacyjnego to Windows 7 z dodatkiem Service Pack 1, Windows 8.1, Windows 10, Windows Server 2008 R2 z dodatkiem SP1, Windows Server 2012, Windows Server 2012 R2, Windows Server 2016 i Windows Server 2019. Instalacja własnego środowiska Integration Runtime na kontrolerze domeny nie jest obsługiwana.
- Wymagany jest .NET Framework 4.6.1 lub nowszy. Jeśli instalujesz własne środowisko Integration Runtime na komputerze z systemem Windows 7, Zainstaluj .NET Framework 4.6.1 lub nowszy. Aby uzyskać szczegółowe informacje, zobacz [wymagania systemowe .NET Framework](/dotnet/framework/get-started/system-requirements) .
- Zalecana konfiguracja dla maszyn wirtualnych środowiska Integration Runtime to co najmniej 2 GHz, cztery rdzenie, 8 GB pamięci RAM i dysk 80 GB.
- Jeśli maszyna hosta jest w stanie hibernacji, własne środowisko Integration Runtime nie odpowiada na żądania danych. Przed zainstalowaniem własnego środowiska Integration Runtime należy skonfigurować odpowiedni plan na komputerze. Jeśli komputer jest skonfigurowany do hibernacji, samodzielna instalacja środowiska Integration Runtime monituje o komunikat.
- Musisz być administratorem na komputerze, aby zainstalować i skonfigurować pomyślnie środowisko Integration Runtime.
- Uruchomienia działania kopiowania odbywają się z określoną częstotliwością. Użycie zasobów (procesor CPU, pamięć) na komputerze jest zgodne z tym samym wzorcem i okresami szczytu. Wykorzystanie zasobów zależy również od intensywnie ilości danych przenoszonych. Gdy trwa wykonywanie wielu zadań kopiowania, zostanie wyświetlone użycie zasobów w godzinach szczytu.
- Zadania mogą zakończyć się niepowodzeniem w przypadku wyodrębnienia danych w formatach Parquet, ORC lub Avro. Tworzenie pliku jest uruchamiane na własnym komputerze integracji i wymaga, aby poniższe wymagania wstępne działały zgodnie z oczekiwaniami (zobacz [Format Parquet w Azure Data Factory](https://docs.microsoft.com/azure/data-factory/format-parquet#using-self-hosted-integration-runtime)).
    - [Pakiet C++ redystrybucyjny Visual 2010](https://download.microsoft.com/download/3/2/2/3224B87F-CFA0-4E70-BDA3-3DE650EFEBA5/vcredist_x64.exe) (x64)
    - Środowisko uruchomieniowe języka Java (JRE) w wersji 8 od dostawcy środowiska JRE, takie jak [przyjęcie OpenJDK](https://adoptopenjdk.net/), co zapewnia, że `JAVA_HOME` zmienna środowiskowa jest ustawiona.

## <a name="installation-best-practices"></a>Najlepsze rozwiązania dotyczące instalacji
Możesz zainstalować własne środowisko Integration Runtime, pobierając pakiet instalacyjny MSI z [Centrum pobierania Microsoft](https://www.microsoft.com/download/details.aspx?id=39717). Instrukcje krok po kroku znajdują się [w artykule Przenoszenie danych między środowiskiem lokalnym i chmurą](tutorial-hybrid-copy-powershell.md) .

- Skonfiguruj plan dodatku na komputerze hosta dla własnego środowiska Integration Runtime, aby komputer nie był w stanie hibernacji. Jeśli maszyna hosta przechodzi w stan hibernacji, środowisko Integration Runtime jest w trybie offline.
- Regularnie Twórz kopie zapasowe poświadczeń skojarzonych z własnym środowiskiem Integration Runtime.
- Aby zautomatyzować operacje konfiguracji samoobsługowego środowiska IR, zapoznaj się z [sekcją poniżej](#automation-support-for-self-hosted-ir-function).  

## <a name="install-and-register-self-hosted-ir-from-the-download-center"></a>Instalowanie i rejestrowanie samodzielnego środowiska IR w centrum pobierania

1. Przejdź do [strony pobierania środowiska Microsoft Integration Runtime](https://www.microsoft.com/download/details.aspx?id=39717).
2. Wybierz pozycję **Pobierz**, wybierz wersję 64-bitową (32-bit nie jest obsługiwana) i wybierz przycisk **dalej**.
3. Uruchom plik MSI bezpośrednio lub Zapisz go na dysku twardym, a następnie uruchom go.
4. Na stronie **powitalnej** wybierz język i wybierz pozycję **dalej**.
5. Zaakceptuj postanowienia licencyjne dotyczące oprogramowania firmy Microsoft i wybierz pozycję **dalej**.
6. Wybierz **folder** , aby zainstalować środowisko Integration Runtime (własne), a następnie wybierz przycisk **dalej**.
7. Na stronie **gotowy do instalacji** wybierz pozycję **Zainstaluj**.
8. Kliknij przycisk **Zakończ** , aby zakończyć instalację.
9. Pobierz klucz uwierzytelniania przy użyciu Azure PowerShell. Oto przykład programu PowerShell służący do pobierania klucza uwierzytelniania:

    ```powershell
    Get-AzDataFactoryV2IntegrationRuntimeKey -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Name $selfHostedIntegrationRuntime
    ```
11. Na stronie **Register Integration Runtime (Self-hosted)** Microsoft Integration Runtime Configuration Manager działającej na maszynie wykonaj następujące czynności:

    a. Wklej klucz uwierzytelniania w obszarze tekstowym.

    b. Opcjonalnie wybierz pozycję **Pokaż klucz uwierzytelniania** , aby wyświetlić tekst klucza.

    c. Wybierz pozycję **Zarejestruj**.

## <a name="automation-support-for-self-hosted-ir-function"></a>Obsługa automatyzacji funkcji samodzielnego środowiska IR


> [!NOTE]
> Jeśli planujesz skonfigurować własne środowisko IR na maszynie wirtualnej platformy Azure i chcesz zautomatyzować konfigurację przy użyciu szablonów Azure Resource Manager, zapoznaj się z [sekcją](#setting-up-a-self-hosted-ir-on-an-azure-vm-by-using-an-azure-resource-manager-template).

Za pomocą wiersza polecenia można skonfigurować lub zarządzać istniejącym własnym, obsługiwanym przez siebie środowisku IR. Można to użyć szczególnie w przypadku automatyzowania instalacji, rejestracji samoobsługowych węzłów IR. 

**Dmgcmd. exe** znajduje się w instalacji samoobsługowej, zazwyczaj z uwzględnieniem: Folder C:\Program Files\Microsoft Integration Runtime\3.0\Shared\. Obsługuje to różne parametry i może być wywoływana za pomocą wiersza polecenia przy użyciu skryptów wsadowych do automatyzacji. 

*Sposób użycia:* 

```powershell
dmgcmd [ -RegisterNewNode "<AuthenticationKey>" -EnableRemoteAccess "<port>" ["<thumbprint>"] -EnableRemoteAccessInContainer "<port>" ["<thumbprint>"] -DisableRemoteAccess -Key "<AuthenticationKey>" -GenerateBackupFile "<filePath>" "<password>" -ImportBackupFile "<filePath>" "<password>" -Restart -Start -Stop -StartUpgradeService -StopUpgradeService -TurnOnAutoUpdate -TurnOffAutoUpdate -SwitchServiceAccount "<domain\user>" ["password"] -Loglevel <logLevel> ] 
```

 *Szczegóły (parametry/Właściwość):* 

| Właściwość                                                    | Opis                                                  | Wymagane |
| ----------------------------------------------------------- | ------------------------------------------------------------ | -------- |
| RegisterNewNode "`<AuthenticationKey>`"                     | Zarejestruj Integration Runtime (Self-hosted) węzeł z określonym kluczem uwierzytelniania | Nie       |
| EnableRemoteAccess "`<port>`" ["`<thumbprint>`"]            | Włącz dostęp zdalny w bieżącym węźle w celu skonfigurowania klastra o wysokiej dostępności i/lub włączenia ustawienia poświadczeń bezpośrednio do samoobsługowego środowiska IR (bez przechodzenia przez usługę ADF) przy użyciu  **Polecenie cmdlet New-AzDataFactoryV2LinkedServiceEncryptedCredential** z komputera zdalnego w tej samej sieci. | Nie       |
| EnableRemoteAccessInContainer "`<port>`" ["`<thumbprint>`"] | Włącz dostęp zdalny do bieżącego węzła, gdy węzeł jest uruchomiony w kontenerze | Nie       |
| DisableRemoteAccess                                         | Wyłącz dostęp zdalny do bieżącego węzła. Dostęp zdalny jest wymagany w przypadku konfiguracji wielowęzłowej. Polecenie cmdlet New-**AzDataFactoryV2LinkedServiceEncryptedCredential** programu PowerShell nadal działa nawet wtedy, gdy dostęp zdalny jest wyłączony o ile jest wykonywany na tym samym komputerze co węzeł samodzielnego środowiska IR. | Nie       |
| Klucz "`<AuthenticationKey>`"                                 | Zastąp/zaktualizuj poprzedni klucz uwierzytelniania. Należy zachować ostrożność, ponieważ może to spowodować, że poprzedni, samodzielny węzeł IR przechodzą do trybu offline, jeśli klucz jest nowym środowiskiem Integration Runtime. | Nie       |
| GenerateBackupFile "`<filePath>`" "`<password>`            | Generuj plik kopii zapasowej dla bieżącego węzła, plik kopii zapasowej zawiera klucz węzła i poświadczenia magazynu danych | Nie       |
| ImportBackupFile "`<filePath>`" "`<password>`              | Przywróć węzeł z pliku kopii zapasowej                          | Nie       |
| Ponowne uruchamianie                                                     | Uruchom ponownie usługę hosta Integration Runtime (Self-hosted)   | Nie       |
| Start                                                       | Uruchom usługę hosta Integration Runtime (Self-hosted)     | Nie       |
| Stop                                                        | Zatrzymaj usługę aktualizacji Integration Runtime (Self-hosted)        | Nie       |
| StartUpgradeService                                         | Uruchom usługę aktualizacji Integration Runtime (Self-hosted)       | Nie       |
| StopUpgradeService                                          | Zatrzymaj usługę aktualizacji Integration Runtime (Self-hosted)        | Nie       |
| TurnOnAutoUpdate                                            | Włącz Integration Runtime (Self-hosted) AutoUpdate        | Nie       |
| TurnOffAutoUpdate                                           | Wyłącz Integration Runtime (Self-hosted) AutoUpdate       | Nie       |
| SwitchServiceAccount "< domena \ użytkownik >" ["hasło"]           | Ustaw DIAHostService do uruchamiania jako nowe konto. Użyj pustego hasła ("") dla konta systemowego lub konta wirtualnego | Nie       |
| Loglevel`<logLevel>`                                       | Ustawianie poziomu dziennika ETW (wyłączone, błąd, pełne lub wszystkie). Zwykle używany przez pomoc techniczną firmy Microsoft podczas debugowania. | Nie       |

   


## <a name="high-availability-and-scalability"></a>Wysoka dostępność i skalowalność
Własne środowisko Integration Runtime może być skojarzone z wieloma maszynami lokalnymi lub Virtual Machines na platformie Azure. Te komputery są nazywane węzłami. Możesz mieć do czterech węzłów skojarzonych z własnym środowiskiem Integration Runtime. Zalety posiadania wielu węzłów (maszyn lokalnych z zainstalowaną bramą) dla bramy logicznej są następujące:
* Wyższa dostępność środowiska Integration Runtime (samodzielne środowisko uruchomieniowe), dzięki czemu nie jest już single point of failure w rozwiązaniu danych Big Data ani integracji danych w chmurze z Azure Data Factory, zapewniając ciągłość z maksymalnie czterema węzłami.
* Zwiększona wydajność i przepływność podczas przenoszenia danych między lokalnymi i magazynami danych w chmurze. Uzyskaj więcej informacji na temat [porównań wydajności](copy-activity-performance.md).

Możesz skojarzyć wiele węzłów, instalując własne oprogramowanie Integration Runtime z [Centrum pobierania](https://www.microsoft.com/download/details.aspx?id=39717). Następnie zarejestruj go przy użyciu dowolnego klucza uwierzytelniania uzyskanego z polecenia cmdlet **New-AzDataFactoryV2IntegrationRuntimeKey** , zgodnie z opisem w [samouczku](tutorial-hybrid-copy-powershell.md).

> [!NOTE]
> Nie musisz tworzyć nowego środowiska Integration Runtime w celu kojarzenia każdego węzła. Możesz zainstalować własne środowisko Integration Runtime na innym komputerze i zarejestrować je przy użyciu tego samego klucza uwierzytelniania. 

> [!NOTE]
> Przed dodaniem kolejnego węzła w celu zapewnienia wysokiej dostępności i skalowalności upewnij się, że opcja **dostęp zdalny do sieci intranet** jest włączona w pierwszym węźle (**Microsoft Integration Runtime** > **Ustawienia** ConfigurationManager >  **Dostęp zdalny do intranetu**). 

### <a name="scale-considerations"></a>Zagadnienia dotyczące skalowania

#### <a name="scale-out"></a>Skalowanie w poziomie

Gdy ilość dostępnej pamięci w samoobsługowym środowisku IR jest niska i użycie procesora CPU jest wysokie, dodanie nowego węzła ułatwia skalowanie obciążenia między maszynami. Jeśli działania zakończą się niepowodzeniem z powodu przekroczenia limitu czasu lub gdy węzeł podczerwieni jest w trybie offline, może to spowodować dodanie węzła do bramy.

#### <a name="scale-up"></a>Skaluj w górę

Gdy dostępna pamięć i procesor CPU nie są używane prawidłowo, ale wykonanie współbieżnych zadań zbliża się do limitu, należy skalować w górę, zwiększając liczbę współbieżnych zadań, które mogą być uruchamiane w węźle. Możesz również skalować w górę, gdy działania przekroczą limit czasu, ponieważ środowisko IR obsługiwane przez siebie jest przeciążone. Jak pokazano na poniższej ilustracji, można zwiększyć maksymalną pojemność węzła:  

![Zwiększanie współbieżnych zadań, które można uruchamiać w węźle](media/create-self-hosted-integration-runtime/scale-up-self-hosted-IR.png)

### <a name="tlsssl-certificate-requirements"></a>Wymagania dotyczące certyfikatu TLS/SSL

Poniżej przedstawiono wymagania dotyczące certyfikatu TLS/SSL, który jest używany do zabezpieczania komunikacji między węzłami Integration Runtime:

- Certyfikat musi być publicznie zaufanym certyfikatem x509 v3. Zalecamy używanie certyfikatów wystawionych przez publiczny (partnerski) urząd certyfikacji.
- Każdy węzeł Integration Runtime musi ufać temu certyfikatowi.
- Nie zalecamy używania certyfikatów alternatywnej nazwy podmiotu (SAN), ponieważ zostanie użyty tylko ostatni element sieci SAN, a wszystkie pozostałe zostaną zignorowane ze względu na bieżące ograniczenia. Jeśli na przykład masz certyfikat sieci SAN, którego sieci San są **Node1.domain.contoso.com** i **Node2.domain.contoso.com**, możesz użyć tego certyfikatu tylko na komputerze, którego nazwa FQDN to **Node2.domain.contoso.com**.
- Certyfikat obsługuje wszelkie rozmiary kluczy obsługiwane przez system Windows Server 2012 R2 dla certyfikatów SSL.
- Certyfikaty używające kluczy CNG nie są obsługiwane.  

> [!NOTE]
> Ten certyfikat służy do szyfrowania portów w węźle samodzielnego środowiska IR używanym do **komunikacji między** węzłami (w przypadku synchronizacji stanu, która obejmuje synchronizację poświadczeń usług połączonych między węzłami) i **przy użyciu polecenia cmdlet programu PowerShell dla programu ustawienie poświadczeń połączonej usługi** z poziomu sieci lokalnej. Zalecamy użycie tego certyfikatu, jeśli środowisko sieci prywatnej nie jest zabezpieczone lub jeśli chcesz zabezpieczyć komunikację między węzłami w sieci prywatnej. Przenoszenie danych z samoobsługowego środowiska IR do innych magazynów danych zawsze odbywa się przy użyciu szyfrowanego kanału, niezależnie od tego, czy ten zestaw certyfikatów jest ustawiony. 

## <a name="sharing-the-self-hosted-integration-runtime-with-multiple-data-factories"></a>Udostępnianie własnego środowiska Integration Runtime z wieloma fabrykami danych

Można ponownie użyć istniejącej infrastruktury środowiska Integration Runtime, która została już skonfigurowana w fabryce danych. Dzięki temu można utworzyć *połączone środowisko Integration Runtime* w innej fabryce danych, odwołując się do istniejącego samodzielnego środowiska IR (udostępnionego).

Aby udostępnić środowisko Integration Runtime (własne) przy użyciu programu PowerShell, zobacz [Tworzenie udostępnionego środowiska Integration Runtime w Azure Data Factory przy użyciu programu PowerShell](create-shared-self-hosted-integration-runtime-powershell.md).

W przypadku 12-minutowego wprowadzenia i pokazania tej funkcji Obejrzyj następujące wideo:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Hybrid-data-movement-across-multiple-Azure-Data-Factories/player]

### <a name="terminology"></a>Terminologia

- **Udostępnione środowisko IR**: Oryginalne środowisko IR, które działa w ramach infrastruktury fizycznej.  
- **Połączony IR**: IR, który odwołuje się do innego udostępnionego środowiska IR. Jest to logiczne środowisko IR i używa infrastruktury innego środowiska IR samoobsługowego (udostępnionego).

### <a name="high-level-steps-for-creating-a-linked-self-hosted-ir"></a>Ogólne kroki tworzenia połączonego własnego środowiska IR

1. W przypadku samodzielnego udostępniania środowiska IR Udziel uprawnień do fabryki danych, w której chcesz utworzyć połączony IR. 

   ![Przycisk do przyznawania uprawnień na karcie Udostępnianie](media/create-self-hosted-integration-runtime/grant-permissions-IR-sharing.png)

   ![Wybory dotyczące przypisywania uprawnień](media/create-self-hosted-integration-runtime/3_rbac_permissions.png)

2. Zanotuj identyfikator zasobu samodzielnego środowiska IR, który ma zostać udostępniony.

   ![Lokalizacja identyfikatora zasobu](media/create-self-hosted-integration-runtime/4_ResourceID_self-hostedIR.png)

3. W fabryce danych, do której udzielono uprawnień, Utwórz nowe środowisko IR (połączone), a następnie wprowadź identyfikator zasobu.

   ![Przycisk służący do tworzenia połączonego środowiska Integration Runtime (własny)](media/create-self-hosted-integration-runtime/6_create-linkedIR_2.png)

   ![Pola nazwy i identyfikatora zasobu](media/create-self-hosted-integration-runtime/6_create-linkedIR_3.png)

### <a name="monitoring"></a>Monitorowanie 

- **Udostępniony IR**

  ![Wybory dotyczące znajdowania udostępnionego środowiska Integration Runtime](media/create-self-hosted-integration-runtime/Contoso-shared-IR.png)

  ![Karta do monitorowania](media/create-self-hosted-integration-runtime/contoso-shared-ir-monitoring.png)

- **Połączony IR**

  ![Opcje znajdowania połączonego środowiska Integration Runtime](media/create-self-hosted-integration-runtime/Contoso-linked-ir.png)

  ![Karta do monitorowania](media/create-self-hosted-integration-runtime/Contoso-linked-ir-monitoring.png)

### <a name="known-limitations-of-self-hosted-ir-sharing"></a>Znane ograniczenia dotyczące samodzielnego udostępniania środowiska IR

* Fabryka danych, w której zostanie utworzone połączone środowisko IR, musi mieć plik [MSI](https://docs.microsoft.com/azure/active-directory/managed-service-identity/overview). Domyślnie fabryki danych utworzone w Azure Portal lub polecenia cmdlet programu PowerShell mają niejawnie utworzony plik MSI. Jednak podczas tworzenia fabryki danych za pomocą szablonu Azure Resource Manager lub zestawu SDK Właściwość **Identity** musi być jawnie ustawiona, aby zapewnić, że Azure Resource Manager tworzy fabrykę danych zawierającą plik msi. 

* Zestaw SDK Azure Data Factory .NET obsługujący tę funkcję jest w wersji 1.1.0 lub nowszej.

* Aby udzielić uprawnienia, użytkownik musi mieć rolę właściciela lub odziedziczonej roli właściciela w fabryce danych, w której istnieje udostępniony środowisko IR.

* Funkcja udostępniania działa tylko dla fabryk danych w ramach tej samej dzierżawy Azure Active Directory.

* W przypadku Active Directory [użytkowników-Gości](https://docs.microsoft.com/azure/active-directory/governance/manage-guest-access-with-access-reviews)funkcja wyszukiwania (wymieniająca wszystkie fabryki danych za pomocą słowa kluczowego wyszukiwania) w interfejsie użytkownika nie [działa](https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-graph-api-permission-scopes#SearchLimits). Jednak o ile użytkownik Gość jest właścicielem fabryki danych, może udostępnić środowisko IR bez funkcji wyszukiwania, bezpośrednio wpisując plik MSI fabryki danych, z którym należy udostępnić środowisko IR w polu tekstowym **Przypisz uprawnienia** i wybierając pozycję **Dodaj** w interfejsie użytkownika Azure Data Factory. 

  > [!NOTE]
  > Ta funkcja jest dostępna tylko w Azure Data Factory v2. 

## <a name="notification-area-icons-and-notifications"></a>Ikony i powiadomienia obszaru powiadomień

Jeśli umieścisz kursor nad ikoną lub komunikatem w obszarze powiadomień, możesz znaleźć szczegółowe informacje o stanie środowiska Integration Runtime (własne środowisko uruchomieniowe).

![Powiadomienia w obszarze powiadomień](media/create-self-hosted-integration-runtime/system-tray-notifications.png)

## <a name="ports-and-firewall"></a>Porty i Zapora
Istnieją dwie zapory, które należy wziąć pod uwagę: *zapora firmowa* uruchomiona na centralnym routerze organizacji oraz *Zapora systemu Windows* skonfigurowana jako demon na komputerze lokalnym, na którym zainstalowano własne środowisko Integration Runtime.

![Zapora](media/create-self-hosted-integration-runtime/firewall.png)

Na poziomie *zapory firmowej* należy skonfigurować następujące domeny i porty wychodzące:

Nazwy domen | Porty | Opis
------------ | ----- | ------------
*.servicebus.windows.net | 443 | Używany do komunikacji z usługą przenoszenia danych zaplecza
*.core.windows.net | 443 | Używany do kopiowania etapowego za pomocą usługi Azure Blob Storage (jeśli jest skonfigurowany)
*.frontend.clouddatahub.net | 443 | Używany do komunikacji z usługą przenoszenia danych zaplecza
download.microsoft.com | 443 | Używane do pobierania aktualizacji

Na poziomie *zapory systemu Windows* (na poziomie komputera) te porty wychodzące są zwykle włączone. W przeciwnym razie domeny i porty można odpowiednio skonfigurować na komputerze, na którym działa środowisko Integration Runtime.

> [!NOTE]
> W oparciu o źródło i ujścia, może być konieczne dozwolonych dodatkowych domen i portów wychodzących w zaporze firmowej lub zaporze systemu Windows.
>
> W przypadku niektórych baz danych w chmurze (na przykład Azure SQL Database i Azure Data Lake) może zajść konieczność dozwolonych adresów IP maszyn wirtualnych środowiska Integration Runtime w konfiguracji zapory.

### <a name="copy-data-from-a-source-to-a-sink"></a>Kopiowanie danych ze źródła do ujścia
Upewnij się, że reguły zapory są prawidłowo włączone w zaporze firmowej, w zaporze systemu Windows na komputerze, na którym działa środowisko Integration Runtime, i w samym magazynie danych. Włączenie tych reguł umożliwia samodzielne środowisko Integration Runtime do pomyślnego nawiązania połączenia ze źródłem i ujściam. Włącz reguły dla wszystkich magazynów danych, które są związane z operacją kopiowania.

Na przykład, aby skopiować z lokalnego magazynu danych do ujścia Azure SQL Database lub ujścia Azure SQL Data Warehouse, wykonaj następujące czynności:

1. Zezwalaj na wychodzącą komunikację TCP na porcie 1433 dla zapory systemu Windows i zapory firmowej.
2. Skonfiguruj ustawienia zapory dla usługi Azure SQL Database, aby dodać adres IP maszyny środowiska Integration Runtime (własny Host) do listy dozwolonych adresów IP.

> [!NOTE]
> Jeśli Zapora nie zezwala na port wychodzący 1433, własne środowisko Integration Runtime nie może bezpośrednio uzyskać dostępu do usługi Azure SQL Database. W takim przypadku można użyć [kopii przygotowanej](copy-activity-performance.md) do Azure SQL Database i Azure SQL Data Warehouse. W tym scenariuszu do przenoszenia danych wymagane jest tylko HTTPS (port 443).


## <a name="proxy-server-considerations"></a>Zagadnienia dotyczące serwera proxy
Jeśli firmowe środowisko sieciowe używa serwera proxy w celu uzyskania dostępu do Internetu, należy skonfigurować własne środowisko Integration Runtime, aby korzystało z odpowiednich ustawień serwera proxy. Serwer proxy można ustawić podczas początkowej fazy rejestracji.

![Określ serwer proxy](media/create-self-hosted-integration-runtime/specify-proxy.png)

Po skonfigurowaniu środowisko Integration runtime używa serwera proxy do łączenia się z usługą w chmurze, źródłem/miejscem docelowym (przy użyciu protokołu HTTP/HTTPS). Jest to wybór opcji **Zmień link** podczas początkowej konfiguracji. Zostanie wyświetlone okno dialogowe Ustawienia proxy.

![Ustaw serwer proxy](media/create-self-hosted-integration-runtime/set-http-proxy.png)

Dostępne są trzy opcje konfiguracji:

- **Nie używaj serwera proxy**: Własne środowisko Integration Runtime nie używa jawnie serwera proxy do łączenia się z usługami w chmurze.
- **Użyj systemowego serwera proxy**: Własne środowisko Integration runtime używa ustawień serwera proxy skonfigurowanych w diahost. exe. config i diawp. exe. config. Jeśli żaden serwer proxy nie jest skonfigurowany w diahost. exe. config i diawp. exe. config, własne środowisko Integration Runtime łączy się bezpośrednio z usługą w chmurze bez przechodzenia przez serwer proxy.
- **Użyj niestandardowego serwera proxy**: Skonfiguruj ustawienia serwera proxy HTTP do użycia na potrzeby własnego środowiska Integration Runtime, zamiast używać konfiguracji w diahost. exe. config i diawp. exe. config. Wymagany jest **adres** i **port** . **Nazwa użytkownika** i **hasło** są opcjonalne w zależności od ustawienia uwierzytelniania serwera proxy. Wszystkie ustawienia są szyfrowane za pomocą funkcji DPAPI systemu Windows w ramach własnego środowiska Integration Runtime i przechowywane lokalnie na komputerze.

Usługa hosta Integration Runtime jest uruchamiana automatycznie po zapisaniu zaktualizowanych ustawień serwera proxy.

Po pomyślnym zarejestrowaniu środowiska Integration Runtime (własne), jeśli chcesz wyświetlić lub zaktualizować ustawienia serwera proxy, użyj Integration Runtime Configuration Manager.

1. Otwórz **Configuration Manager Microsoft Integration Runtime**.
2. Przejdź do karty **Ustawienia**.
3. Wybierz łącze **Zmień** w sekcji **serwer proxy HTTP** , aby otworzyć okno dialogowe **Ustawianie serwera proxy HTTP** .
4. Wybierz opcję **Dalej**. Następnie zostanie wyświetlone ostrzeżenie z prośbą o zgodę na zapisanie ustawienia serwera proxy i ponowne uruchomienie usługi hosta Integration Runtime.

Serwer proxy HTTP można wyświetlić i zaktualizować za pomocą narzędzia Configuration Manager.

![Wyświetl serwer proxy](media/create-self-hosted-integration-runtime/view-proxy.png)

> [!NOTE]
> W przypadku skonfigurowania serwera proxy z uwierzytelnianiem NTLM usługa hosta Integration Runtime jest uruchamiana w ramach konta domeny. Jeśli zmienisz hasło konta domeny później, pamiętaj, aby zaktualizować ustawienia konfiguracji usługi i ponownie je uruchomić. Ze względu na to wymaganie zalecamy użycie dedykowanego konta domeny w celu uzyskania dostępu do serwera proxy, który nie wymaga częstego aktualizowania hasła.

### <a name="configure-proxy-server-settings"></a>Skonfiguruj ustawienia serwera proxy

W przypadku wybrania ustawienia **Użyj serwera proxy** dla serwera proxy HTTP, własne środowisko Integration runtime używa ustawienia proxy w diahost. exe. config i diawp. exe. config. Jeśli żaden serwer proxy nie został określony w diahost. exe. config i diawp. exe. config, środowisko Integration Runtime (własne) łączy się bezpośrednio z usługą w chmurze bez przechodzenia przez serwer proxy. Poniższa procedura zawiera instrukcje dotyczące aktualizowania pliku diahost. exe. config:

1. W Eksploratorze plików wykonaj bezpieczną kopię folderu C:\Program Files\Microsoft Integration Runtime\3.0\Shared\diahost.exe.config, aby utworzyć kopię zapasową oryginalnego pliku.
2. Otwórz program Notepad. exe uruchomiony jako administrator i Otwórz plik tekstowy C:\Program Files\Microsoft Integration Runtime\3.0\Shared\diahost.exe.config. Znajdź domyślny tag dla system.net, jak pokazano w poniższym kodzie:

    ```xml
    <system.net>
        <defaultProxy useDefaultCredentials="true" />
    </system.net>
    ```
    Następnie można dodać Szczegóły serwera proxy, jak pokazano w następującym przykładzie:

    ```xml
    <system.net>
        <defaultProxy enabled="true">
              <proxy bypassonlocal="true" proxyaddress="http://proxy.domain.org:8888/" />
        </defaultProxy>
    </system.net>
    ```

    Dodatkowe właściwości są dozwolone wewnątrz tagu proxy w celu określenia wymaganych ustawień, takich `scriptLocation`jak. Zobacz [element proxy (ustawienia sieciowe)](https://msdn.microsoft.com/library/sa91de1e.aspx) dla składni.

    ```xml
    <proxy autoDetect="true|false|unspecified" bypassonlocal="true|false|unspecified" proxyaddress="uriString" scriptLocation="uriString" usesystemdefault="true|false|unspecified "/>
    ```
3. Zapisz plik konfiguracji w oryginalnej lokalizacji. Następnie uruchom ponownie usługę hosta Integration Runtime (samodzielne), która pobiera zmiany. 

   Aby ponownie uruchomić usługę, użyj apletu usługi w panelu sterowania. Lub z Integration Runtime Configuration Manager wybierz przycisk **Zatrzymaj usługę** , a następnie wybierz pozycję **Uruchom usługę**. 
   
   Jeśli usługa nie zostanie uruchomiona, prawdopodobnie w edytowanym pliku konfiguracyjnym aplikacji dodano niepoprawną składnię tagu XML.

> [!IMPORTANT]
> Nie zapomnij zaktualizować obu diahost. exe. config i diawp. exe. config.

Należy również upewnić się, że Microsoft Azure znajduje się w dozwolonych firmy. Listę prawidłowych adresów IP Microsoft Azure można pobrać z [Centrum pobierania Microsoft](https://www.microsoft.com/download/details.aspx?id=41653).

### <a name="possible-symptoms-for-firewall-and-proxy-server-related-issues"></a>Ewentualne objawy dotyczące problemów z zaporą i serwerem proxy
Jeśli wystąpią błędy podobne do następujących, prawdopodobnie jest to spowodowane nieprawidłową konfiguracją zapory lub serwera proxy, co uniemożliwia samodzielne środowisko Integration Runtime łączenie się z usługą Data Factory w celu samodzielnego uwierzytelnienia. Aby upewnić się, że Zapora i serwer proxy są prawidłowo skonfigurowane, zapoznaj się z poprzednią sekcją.

* Podczas próby zarejestrowania własnego środowiska Integration Runtime jest wyświetlany następujący błąd: "Nie można zarejestrować tego węzła Integration Runtime! Upewnij się, że klucz uwierzytelniania jest prawidłowy, a na tym komputerze jest uruchomiona usługa hosta usługi integracji.
* Po otwarciu Integration Runtime Configuration Manager zostanie wyświetlony stan **odłączony** lub **nawiązanie połączenia**. Podczas wyświetlania dzienników zdarzeń systemu Windows, w obszarze **Podgląd zdarzeń** >  > **Dzienniki aplikacji i usług** **Microsoft Integration Runtime**, zobaczysz komunikaty o błędach podobne do tego:

    ```
    Unable to connect to the remote server
    A component of Integration Runtime has become unresponsive and restarts automatically. Component name: Integration Runtime (Self-hosted).
    ```

### <a name="enabling-remote-access-from-an-intranet"></a>Włączanie dostępu zdalnego z intranetu  
W przypadku korzystania z programu PowerShell w celu szyfrowania poświadczeń z innego komputera (w sieci), w którym jest zainstalowany własny środowisko Integration Runtime, można włączyć opcję **dostęp zdalny z intranetu** . Jeśli program PowerShell zostanie uruchomiony w celu szyfrowania poświadczeń na tym samym komputerze, na którym zainstalowano własne środowisko Integration Runtime, nie można włączyć **dostępu zdalnego z intranetu**.

Należy włączyć **dostęp zdalny z intranetu** przed dodaniem kolejnego węzła w celu zapewnienia wysokiej dostępności i skalowalności.  

Podczas samodzielnej instalacji środowiska Integration Runtime (w wersji 3.3. xxxx. x nowszej) domyślnie, samoobsługowe Instalowanie środowiska Integration Runtime wyłącza **dostęp zdalny z intranetu** na własnym komputerze Integration Runtime.

Jeśli używasz zapory innej firmy, możesz ręcznie otworzyć port 8060 (lub port skonfigurowany przez użytkownika). Jeśli podczas konfigurowania własnego środowiska Integration Runtime występuje problem z zaporą, spróbuj użyć następującego polecenia, aby zainstalować własne środowisko Integration Runtime bez konfigurowania zapory:

```
msiexec /q /i IntegrationRuntime.msi NOFIREWALL=1
```

Jeśli nie zdecydujesz się na otwarcie portu 8060 na komputerze, na którym działa środowisko Integration Runtime, użyj mechanizmów innych niż ustawienia aplikacja poświadczeń, aby skonfigurować poświadczenia magazynu danych. Można na przykład użyć polecenia cmdlet **New-AzDataFactoryV2LinkedServiceEncryptCredential** programu PowerShell.


## <a name="next-steps"></a>Następne kroki
Instrukcje krok po kroku znajdują się w następującym samouczku: [Samouczek: Kopiowanie danych lokalnych do chmury](tutorial-hybrid-copy-powershell.md).
