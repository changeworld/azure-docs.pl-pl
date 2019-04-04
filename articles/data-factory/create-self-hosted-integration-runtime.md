---
title: Tworzenie własnego środowiska integration runtime w usłudze Azure Data Factory | Dokumentacja firmy Microsoft
description: Dowiedz się, jak utworzyć własne środowisko integration runtime w usłudze Azure Data Factory, która umożliwia fabryki danych mieć dostęp do magazynów danych w sieci prywatnej.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/15/2019
author: nabhishek
ms.author: abnarain
manager: craigg
ms.openlocfilehash: 6ab5ee923cc439901149a26d7af4b57f9933ee19
ms.sourcegitcommit: 9f4eb5a3758f8a1a6a58c33c2806fa2986f702cb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2019
ms.locfileid: "58905889"
---
# <a name="create-and-configure-a-self-hosted-integration-runtime"></a>Tworzenie i konfigurowanie własnego środowiska integration runtime
Środowisko integration runtime (IR) to infrastruktura obliczeniowa, która używa usługi Azure Data Factory w celu zapewnienia możliwości integracji danych w różnych środowiskach sieciowych. Aby uzyskać szczegółowe informacje o środowisku IR, zobacz [Omówienie środowiska Integration runtime](concepts-integration-runtime.md).

Własne środowisko integration runtime można uruchomić działania kopiowania między magazynem danych w chmurze i magazynem danych w sieci prywatnej, a jej wysyłania działań przekształcania dla zasobów obliczeniowych w między siecią lokalną lub siecią wirtualną platformy Azure. Wymaga instalacji własnego środowiska integration runtime na maszynie lokalnej lub maszynę wirtualną (VM) w sieci prywatnej.  

W tym dokumencie opisano, jak utworzyć i skonfigurować samodzielnie hostowane podczerwieni.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="high-level-steps-to-install-a-self-hosted-ir"></a>Ogólne kroki, aby zainstalować własnego środowiska IR
1. Utwórz własne środowisko Integration Runtime. Interfejs użytkownika usługi Azure Data Factory służy do tego zadania. Oto przykład programu PowerShell:

    ```powershell
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Name $selfHostedIntegrationRuntimeName -Type SelfHosted -Description "selfhosted IR description"
    ```
  
2. [Pobierz](https://www.microsoft.com/download/details.aspx?id=39717) i zainstaluj własne środowisko integration runtime na maszynie lokalnej.

3. Pobierz klucz uwierzytelniania i zarejestrowania własnego środowiska integration runtime przy użyciu klucza. Oto przykład programu PowerShell:

    ```powershell
    Get-AzDataFactoryV2IntegrationRuntimeKey -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Name $selfHostedIntegrationRuntime.  
    ```

## <a name="setting-up-a-self-hosted-ir-on-an-azure-vm-by-using-an-azure-resource-manager-template-automation"></a>Konfigurowanie własnego środowiska IR na Maszynie wirtualnej platformy Azure przy użyciu szablonu usługi Azure Resource Manager (automation)
Instalacja Self-Hosted IR na maszynie wirtualnej platformy Azure można zautomatyzować za pomocą [tego szablonu usługi Azure Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vms-with-selfhost-integration-runtime). Ten szablon zawiera prosty sposób, aby w pełni funkcjonalne może być samodzielnie hostowane środowisko IR wewnątrz sieci wirtualnej platformy Azure za pomocą funkcji wysokiej dostępności i skalowalności (tak długo, jak ustawić jest liczba węzłów do 2 lub nowszego).

## <a name="command-flow-and-data-flow"></a>Polecenie przepływu i przepływu danych
Podczas przenoszenia danych między środowiska lokalnego i chmury, działania używa własnego środowiska integration runtime, aby przenieść dane z lokalnego źródła danych w chmurze i na odwrót.

Poniżej przedstawiono przepływ danych wysokiego poziomu dla podsumowania czynności kopiowania przy użyciu własnego środowiska IR:

![Ogólne omówienie](media/create-self-hosted-integration-runtime/high-level-overview.png)

1. Dane i deweloperów tworzy własne środowisko integration runtime w fabryce danych Azure przy użyciu polecenia cmdlet programu PowerShell. Witryna Azure portal nie obsługuje obecnie tej funkcji.
2. Dane i deweloperów tworzy połączonej usługi magazynu danych w środowisku lokalnym za pośrednictwem własne wystąpienie infrastruktury integration runtime za pomocą której należy połączyć się z magazynów danych.
3. Węzeł Self-Hosted integration runtime szyfruje poświadczenia za pomocą Windows Data Protection interfejsu API (DPAPI) i zapisuje poświadczenia lokalnie. Jeśli wiele węzłów są skonfigurowane w celu zapewnienia wysokiej dostępności, poświadczenia są dodatkowo synchronizowane w innych węzłach. Każdy węzeł szyfruje poświadczenia przy użyciu interfejsu DPAPI i przechowuje je lokalnie. Synchronizacja poświadczeń jest niewidoczna dla dewelopera danych i jest obsługiwany przez Self-Hosted IR    
4. Usługa Data Factory, który komunikuje się z własnego środowiska integration runtime do planowania i zarządzania zadań za pośrednictwem *kanał kontrolny* , który używa udostępnionych kolejki usługi Azure Service Bus. Gdy zadanie działania musi zostać uruchomione, usługi Data Factory umieszcza w kolejce żądań wraz z żadnych informacji dotyczących poświadczeń (w przypadku, gdy poświadczenia nie są już przechowywane na własne środowisko integration runtime). Własne środowisko integration runtime dotyczącego zadania po sondowania kolejki.
5. Własne środowisko integration runtime kopiuje dane z lokalnego magazynu do magazynu w chmurze lub na odwrót w zależności od sposobu skonfigurowania działania kopiowania w potoku danych. W tym kroku własnego środowiska integration runtime bezpośrednio komunikuje się z usługami magazynu w chmurze, takimi jak usługi Azure Blob storage za pośrednictwem bezpiecznego kanału (HTTPS).

## <a name="considerations-for-using-a-self-hosted-ir"></a>Zagadnienia dotyczące korzystania z własnego środowiska IR

- Pojedynczy własnego środowiska integration runtime może służyć do wielu źródeł danych w środowisku lokalnym. Pojedynczy własnego środowiska integration runtime mogą być udostępniane innym fabryki danych w ramach tej samej dzierżawie usługi Azure Active Directory. Aby uzyskać więcej informacji, zobacz [udostępnianie własnego środowiska integration runtime](#sharing-the-self-hosted-integration-runtime-with-multiple-data-factories).
- Może mieć tylko jedno wystąpienie własnego środowiska integration runtime zainstalowane na jednym komputerze. W przypadku dwóch fabryki danych, które chcą korzystać z lokalnych źródeł danych, musisz zainstalować własnego środowiska integration runtime na dwóch lokalnych komputerów każdego z fabryki danych, lub użyj [własne środowisko IR do udostępniania funkcji](#sharing-the-self-hosted-integration-runtime-with-multiple-data-factories)udostępniać własne środowisko integration runtime z innej usługi Data Factory.  
- Własne środowisko integration runtime nie musi znajdować się na tym samym komputerze co źródło danych. Jednak bliżej źródła danych o własne środowisko integration runtime skraca czas dla własnego środowiska integration runtime do połączenia ze źródłem danych. Zaleca się zainstalowanie własnego środowiska integration runtime na maszynie, która jest inna niż wersja tego źródła danych lokalnych hostów. W przypadku Self-Hosted integration runtime i źródła danych na różnych maszynach, własne środowisko integration runtime nie konkurują o zasoby ze źródłem danych.
- Na różnych maszynach, łączących się z tego samego źródła danych w środowisku lokalnym, możesz mieć wiele środowisk Self-Hosted integration Runtime. Na przykład Niewykluczone, że dwa produkty Self-Hosted integration Runtime, które obsługują dwa fabryki danych, ale tego samego źródła danych lokalnych jest zarejestrowana przy użyciu fabryk danych.
- Jeśli masz już zainstalowany na komputerze, aby obsługiwać scenariusz, w usłudze Power BI bramy, zainstalować osobne własnego środowiska integration runtime usługi Azure Data Factory na innym komputerze.
- Własne środowisko integration runtime, należy użyć do obsługi integracji danych w ramach sieci wirtualnej platformy Azure.
- Źródło danych należy traktować jako źródła danych w środowisku lokalnym, który znajduje się za zaporą, nawet wtedy, gdy korzystasz z usługi Azure ExpressRoute. Użyj własnego środowiska integration runtime można ustanowić łączności między usługą i źródła danych.
- Należy użyć własnego środowiska integration runtime, nawet jeśli magazyn danych znajduje się w chmurze na maszynie wirtualnej IaaS platformy Azure.
- Zadania może się nie powieść w własnego środowiska integration runtime zainstalowanego na serwerze Windows, na które zgodne ze standardem FIPS jest włączone szyfrowanie. Aby obejść ten problem, należy wyłączyć szyfrowanie zgodne ze standardem FIPS na serwerze. Aby wyłączyć szyfrowanie zgodne ze standardem FIPS, należy zmienić następującą wartość rejestru z zakresu od 1 (włączone) na wartość 0 (wyłączone): `HKLM\System\CurrentControlSet\Control\Lsa\FIPSAlgorithmPolicy\Enabled`.

## <a name="prerequisites"></a>Wymagania wstępne

- Dostępne są wersje obsługiwany system operacyjny: Windows 7 z dodatkiem Service Pack 1, Windows 8.1, systemu Windows 10, Windows Server 2008 R2 z dodatkiem SP1, Windows Server 2012, Windows Server 2012 R2 i systemu Windows Server 2016. Instalacja z własnego środowiska integration runtime na kontrolerze domeny nie jest obsługiwana.
- Program .NET framework 4.6.1 lub nowszej jest wymagana. Jeśli instalujesz własnego środowiska integration runtime na komputerze z Windows 7, zainstaluj program .NET Framework 4.6.1 lub nowszej. Zobacz [.NET Framework System Requirements](/dotnet/framework/get-started/system-requirements) Aby uzyskać szczegółowe informacje.
- Zalecana konfiguracja maszyny Self-Hosted integration runtime jest co najmniej 2 GHz, cztery rdzenie, 8 GB pamięci RAM i 80GB dysku.
- Jeśli komputer hosta przechodzi w stan hibernacji, własne środowisko integration runtime nie odpowiada na żądania danych. Na komputerze, należy skonfigurować plan zasilania odpowiednich, przed zainstalowaniem własnego środowiska integration runtime. Jeśli komputer jest skonfigurowany do hibernacji, instalacja Self-Hosted integration runtime wyświetli komunikat.
- Musi być administratorem na komputerze, aby zainstalować i skonfigurować własne środowisko integration runtime pomyślnie.
- Uruchomienia działania kopiowania powinny być wykonywane dla określonej częstotliwości. Użycie zasobów (procesor CPU, pamięci) na maszynie jest zgodna z szczytowe i czasach bezczynności tego samego wzorca. Wykorzystanie zasobów zależy również od intensywnie ilości danych przenoszonych. W przypadku wielu zadań kopiowania w toku, zobaczysz użycia zasobów wzrosną godzinami szczytu.

## <a name="installation-best-practices"></a>Najlepsze rozwiązania dotyczące instalacji
Własne środowisko integration runtime można zainstalować, pobierając pakietu Instalatora MSI z [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=39717). Zobacz [przenoszenie danych między lokalizacją lokalną i chmurą artykułu](tutorial-hybrid-copy-powershell.md) instrukcje krok po kroku.

- Skonfigurować plan zasilania na komputerze hosta dla własnego środowiska integration runtime, aby komputer nie hibernacji. Jeśli komputer hosta przechodzi w stan hibernacji, własne środowisko integration runtime przejdzie do trybu offline.
- Utwórz kopię zapasową poświadczeń skojarzonych z własnego środowiska integration runtime regularnie.

## <a name="install-and-register-self-hosted-ir-from-the-download-center"></a>Zainstalowanie i zarejestrowanie własne środowisko IR w Centrum pobierania

1. Przejdź do [strony pobierania środowiska uruchomieniowego integracji Microsoft](https://www.microsoft.com/download/details.aspx?id=39717).
2. Wybierz **Pobierz**, wybierz wersję 64-bitowych (32-bitowych nie jest obsługiwana) i wybierz **dalej**.
3. Uruchom plik MSI, bezpośrednio lub zapisz go na dysku twardym i uruchom go.
4. Na **powitalnej** strony, wybierz język i wybierz **dalej**.
5. Zaakceptuj postanowienia licencyjne dotyczące oprogramowania firmy Microsoft i wybierz **dalej**.
6. Wybierz **folderu** Zainstaluj własne środowisko integration runtime i wybierz **dalej**.
7. Na **gotowe do zainstalowania** wybierz opcję **zainstalować**.
8. Kliknij przycisk **Zakończ** do ukończenia instalacji.
9. Pobierz klucz uwierzytelniania przy użyciu programu Azure PowerShell. Poniżej przedstawiono przykład programu PowerShell do pobierania klucza uwierzytelniania:

    ```powershell
    Get-AzDataFactoryV2IntegrationRuntimeKey -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Name $selfHostedIntegrationRuntime
    ```
11. Na **rejestrowanie produktu Integration Runtime (Self-Hosted)** strony programu Microsoft Integration Runtime Configuration Manager uruchomiony na Twoim komputerze, wykonaj następujące czynności:

    a. W obszarze tekstowym, Wklej klucz uwierzytelniania.

    b. Opcjonalnie można zaznaczyć **klucz uwierzytelniania Show** Aby wyświetlić tekst klucza.

    c. Wybierz pozycję **Zarejestruj**.


## <a name="high-availability-and-scalability"></a>Wysoka dostępność i skalowalność
Własne środowisko integration runtime może być skojarzony z wieloma maszynami w środowisku lokalnym. Te maszyny są nazywane węzłami. Może mieć maksymalnie cztery węzły skojarzone z własnego środowiska integration runtime. Zalety o wielu węzłach (maszynach lokalnych za pośrednictwem bramy, zainstalować) dla bramy sieci logiczne to:
* Wyższą dostępność własnego środowiska integration runtime, tak że użytkownika nie jest już pojedynczym punktem awarii w danych big data rozwiązania lub w chmurze integrację danych dzięki usłudze Azure Data Factory, zapewniając ciągłość działania w przypadku maksymalnie czterech węzłów.
* Zwiększona wydajność i przepływność podczas przenoszenia danych między lokalizacją lokalną i chmurą magazynów danych. Uzyskaj więcej informacji [porównania wydajności](copy-activity-performance.md).

Można skojarzyć wiele węzłów, instalując oprogramowanie Self-Hosted integration runtime z [Centrum pobierania](https://www.microsoft.com/download/details.aspx?id=39717). Następnie, zarejestruj go przy użyciu klucza uwierzytelniania uzyskanego z **New AzDataFactoryV2IntegrationRuntimeKey** polecenia cmdlet, zgodnie z opisem w [samouczek](tutorial-hybrid-copy-powershell.md).

> [!NOTE]
> Nie ma potrzeby tworzenia nowych własnego środowiska integration runtime do kojarzenia każdego węzła. Można zainstalować własnego środowiska integration runtime na inny komputer i zarejestrować go za pomocą tego samego klucza uwierzytelniania. 

> [!NOTE]
> Przed dodaniem innego węzła o wysokiej dostępności i skalowalności, upewnij się, że **dostęp zdalny do intranetu** opcja jest włączona na pierwszym węźle (**Menedżera konfiguracji produktu Integration Runtime firmy Microsoft**  >  **Ustawienia** > **dostęp zdalny do intranetu**). 

### <a name="scale-considerations"></a>Zagadnienia dotyczące skalowania

#### <a name="scale-out"></a>Skalowanie w poziomie

Gdy ilość dostępnej pamięci na własne środowisko IR jest niska, a użycie procesora CPU jest wysokie, dodanie nowego węzła ułatwia skalowanie obciążenia między maszynami. Jeśli działania kończą się niepowodzeniem, ponieważ jesteś limit czasu lub ponieważ samodzielnie hostowany węzeł IR jest w trybie offline, pomaga Jeśli dodasz węzeł do bramy.

#### <a name="scale-up"></a>Skalowanie w górę

Gdy ilość dostępnej pamięci i procesora CPU nie mogą być wykorzystane dobrze, ale wykonanie równoczesnych zadań zbliża się limit, powinien skalowanie w górę, zwiększając liczbę współbieżnych zadań, które można uruchomić w węźle. Można również skalować w górę, gdy działania przekraczają limit, ponieważ własne środowisko IR jest przeciążony. Jak pokazano na poniższej ilustracji, można zwiększyć maksymalną pojemność dla węzła:  

![Zwiększenie równoczesnych zadań, które można uruchomić w węźle](media/create-self-hosted-integration-runtime/scale-up-self-hosted-IR.png)

### <a name="tlsssl-certificate-requirements"></a>Wymagania certyfikatu TLS/SSL

Poniżej przedstawiono wymagania dotyczące certyfikatu TLS/SSL, używany do zabezpieczania komunikacji między integration runtime węzłów:

- Certyfikat musi być publicznie zaufany X509 certyfikatu v3. Zalecane jest użycie certyfikatów wystawionych przez publiczny (partnerów) urząd certyfikacji (CA).
- Każdy węzeł środowiska integration runtime, muszą ufać temu certyfikatowi.
- Nie zaleca certyfikatów alternatywnej nazwy podmiotu (SAN), ponieważ będzie służyć tylko ostatni element sieci SAN, a wszystkie pozostałe zostaną zignorowane ze względu na bieżące ograniczenia Na przykład, jeśli masz certyfikat SAN sieci SAN, które **node1.domain.contoso.com** i **node2.domain.contoso.com**, możesz użyć tego certyfikatu tylko na komputerze, którego nazwa FQDN jest  **Node2.domain.contoso.com**.
- Certyfikat obsługuje wszystkie rozmiar klucza obsługiwana przez system Windows Server 2012 R2 dla certyfikatów SSL.
- Certyfikaty, które używają kluczy CNG nie są obsługiwane.  

> [!NOTE]
> Ten certyfikat jest używany do szyfrowania portów w węźle Self-Hosted IR, umożliwiający **komunikacji między węzłami** (dla synchronizacji stanu, który zawiera połączone usługi poświadczenia synchronizacji między węzłami) oraz w trakcie **przy użyciu programu PowerShell polecenia cmdlet dla połączonej usługi poświadczeń ustawienie** z w obrębie sieci lokalnej. Zalecane jest używanie tego certyfikatu, jeśli prywatnego środowiska sieciowego nie jest bezpieczna lub jeśli chcesz do zabezpieczenia komunikacji między węzłami w ramach sieci prywatnej, jak również. Przenoszenie danych przesyłanych z własne środowisko IR w innych magazynach danych występuje zawsze, przy użyciu szyfrowanego kanału, niezależnie od tego certyfikatu, lub nie. 

## <a name="sharing-the-self-hosted-integration-runtime-with-multiple-data-factories"></a>Udostępnianie własnego środowiska integration runtime z różnych fabryk danych

Można ponownie użyć istniejącej infrastrukturze środowiska uruchomieniowego Self-Hosted integration masz już skonfigurowane w usłudze data factory. Dzięki temu można utworzyć *połączone własnego środowiska integration runtime* w różnych danych fabryki, odwołując się do istniejącego własne środowisko IR (udostępniony).

Aby udostępnić własne środowisko integration runtime przy użyciu programu PowerShell, zobacz [Tworzenie udostępnionego własnego środowiska integration runtime w usłudze Azure Data Factory przy użyciu programu PowerShell](create-shared-self-hosted-integration-runtime-powershell.md).

Wprowadzenie 12 minutowy i pokaz działania tej funkcji Obejrzyj poniższy film wideo:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Hybrid-data-movement-across-multiple-Azure-Data-Factories/player]

### <a name="terminology"></a>Terminologia

- **Udostępnione środowisko IR**: Oryginalny może być samodzielnie hostowane środowisko IR, który działa na infrastruktury fizycznej.  
- **Połączone środowisko IR**: Środowisko IR, który odwołuje się do innego udostępnionego podczerwieni. Jest logiczną IR, używana jest infrastruktura innego własne środowisko IR (udostępniana).

### <a name="high-level-steps-for-creating-a-linked-self-hosted-ir"></a>Ogólne kroki do tworzenia połączonej własnego środowiska IR

1. W własne środowisko IR do udostępnienia należy przyznać uprawnienia do usługi data factory, w której chcesz utworzyć połączone podczerwieni. 

   ![Przycisk przyznawania uprawnień na karcie Udostępnianie](media/create-self-hosted-integration-runtime/grant-permissions-IR-sharing.png)

   ![Wybory dotyczące przypisywania uprawnień](media/create-self-hosted-integration-runtime/3_rbac_permissions.png)

2. Należy pamiętać, identyfikator zasobu własne środowisko IR do udostępnienia.

   ![Lokalizacja Identyfikatora zasobu](media/create-self-hosted-integration-runtime/4_ResourceID_self-hostedIR.png)

3. W fabryki danych, do którego zostały przyznane uprawnienia Utwórz nowy własnego środowiska IR (połączony), a następnie wprowadź identyfikator zasobu.

   ![Przycisk tworzenia połączonej własnego środowiska integration runtime](media/create-self-hosted-integration-runtime/6_create-linkedIR_2.png)

   ![Pola Nazwa i identyfikator zasobu](media/create-self-hosted-integration-runtime/6_create-linkedIR_3.png)

### <a name="monitoring"></a>Monitorowanie 

- **Udostępnione środowisko IR**

  ![Opcje służące do znajdowania udostępnionego IR](media/create-self-hosted-integration-runtime/Contoso-shared-IR.png)

  ![Karta monitorowania](media/create-self-hosted-integration-runtime/contoso-shared-ir-monitoring.png)

- **Połączone środowisko IR**

  ![Opcje służące do znajdowania połączone IR](media/create-self-hosted-integration-runtime/Contoso-linked-ir.png)

  ![Karta monitorowania](media/create-self-hosted-integration-runtime/Contoso-linked-ir-monitoring.png)

### <a name="known-limitations-of-self-hosted-ir-sharing"></a>Znane ograniczenia dotyczące udostępniania samodzielnie hostowanego środowiska IR

* Fabryki danych, w której zostaną utworzone połączone środowisko IR musi mieć [MSI](https://docs.microsoft.com/azure/active-directory/managed-service-identity/overview). Domyślnie fabryk danych utworzone w witrynie Azure portal lub poleceń cmdlet programu PowerShell mają Instalatora MSI tworzone niejawnie. Jednak po utworzeniu fabryki danych przy użyciu szablonu usługi Azure Resource Manager lub zestaw SDK **tożsamości** właściwość musi być ustawiona jawnie, aby upewnić się, że usługi Azure Resource Manager do tworzenia fabryki danych, zawierającą Instalatora MSI. 

* Azure Data Factory zestawu .NET SDK obsługującej tę funkcję jest wersji 1.1.0 lub nowszej.

* Aby udzielić uprawnień, użytkownik musi roli właściciela lub dziedziczone właściciela w usłudze data factory, w której istnieje IR udostępnionych.

* Funkcja udostępniania działa tylko dla fabryki danych w ramach tej samej dzierżawie usługi Azure Active Directory.

* Usługi Active Directory [użytkowników-gości](https://docs.microsoft.com/azure/active-directory/governance/manage-guest-access-with-access-reviews), funkcja wyszukiwania (wyświetlanie listy wszystkich fabryk danych przy użyciu słowa kluczowe do wyszukania) w interfejsie użytkownika [nie działa](https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-graph-api-permission-scopes#SearchLimits). Ale tak długo, jak użytkownik-Gość jest właścicielem usługi data factory, mogą oni udostępnić środowisko IR bez funkcji wyszukiwania bezpośrednio, wpisując MSI fabryki danych, z którym musi być udostępniona w środowisko IR **przypisać uprawnienia** pole tekstowe i Wybieranie **Dodaj** w interfejsie użytkownika usługi Azure Data Factory. 

  > [!NOTE]
  > Ta funkcja jest dostępna tylko w usłudze Azure Data Factory V2. 

## <a name="notification-area-icons-and-notifications"></a>Ikony obszaru powiadomień i powiadomienia

Jeśli przesuniesz kursor nad ikonę lub komunikat w obszarze powiadomień można znaleźć szczegółowe informacje o stanie własnego środowiska integration runtime.

![Powiadomienia w obszarze powiadomień](media/create-self-hosted-integration-runtime/system-tray-notifications.png)

## <a name="ports-and-firewall"></a>Porty i zapory
Istnieją dwa zaporach, aby wziąć pod uwagę: *zaporą firmową* uruchomionego na routerze centralnej w organizacji i *zapory Windows* skonfigurowany jako demon na komputerze lokalnym gdzie własne środowisko integration runtime jest zainstalowany.

![Zapora](media/create-self-hosted-integration-runtime/firewall.png)

W *zaporą firmową* poziomu, należy skonfigurować następujące domeny i portów wychodzących:

Nazwy domen | Porty | Opis
------------ | ----- | ------------
*.servicebus.windows.net | 443 | Używany do komunikacji z usługą przenoszenia danych zaplecza
*.core.windows.net | 443 | Używany do kopiowania przejściowego za pośrednictwem usługi Azure Blob storage (jeśli jest skonfigurowane)
*.frontend.clouddatahub.net | 443 | Używany do komunikacji z usługą przenoszenia danych zaplecza
download.microsoft.com | 443 | Używane do pobierania aktualizacji

W *zapory Windows* poziomie (komputer), te porty wychodzące zwykle są włączone. Nie można skonfigurować w domenach i porty odpowiednio na maszynie Self-Hosted integration runtime.

> [!NOTE]
> Na podstawie źródła i ujścia, może być konieczne dodatkowe domeny listy dozwolonych adresów i portów wychodzących w firmowej zapory lub zaporę Windows.
>
> Dla niektórych baz danych w chmurze (na przykład usługi Azure SQL Database i Azure Data Lake) może być konieczne do listy dozwolonych adresów IP Self-Hosted integration runtime maszyn na ich konfigurację zapory.

### <a name="copy-data-from-a-source-to-a-sink"></a>Kopiowanie danych ze źródła do ujścia
Upewnij się, że reguły zapory są prawidłowo włączona zapory firmowej zapory Windows na maszynie Self-Hosted integration runtime i sam magazyn danych. Włączenie tych zasad umożliwia własnego środowiska integration runtime do łączenia się zarówno źródła i ujścia pomyślnie. Włącz reguły dla każdego magazynu danych, który jest używany w operacji kopiowania.

Na przykład aby skopiować z lokalnego magazynu danych ujścia Azure SQL Database lub ujścia Azure SQL Data Warehouse, wykonaj następujące czynności:

1. Zezwól na komunikację TCP na porcie 1433 dla zapory Windows i zaporą firmową.
2. Konfigurowanie ustawień zapory bazy danych Azure SQL, aby dodać adres IP komputera Self-Hosted integration runtime do listy dozwolonych adresów IP.

> [!NOTE]
> Jeśli Zapora nie zezwala na wychodząca przez port 1433, własne środowisko integration runtime nie może uzyskać dostępu danych Azure SQL database bezpośrednio. W takim przypadku można użyć [kopiowania etapowego](copy-activity-performance.md) do usługi Azure SQL Database i Azure SQL Data Warehouse. W tym scenariuszu wymagałoby HTTPS (port 443) w przypadku przenoszenia danych.


## <a name="proxy-server-considerations"></a>Zagadnienia dotyczące serwera proxy
Jeśli w środowisku firmowym sieci korzysta z serwera proxy, dostęp do Internetu, należy skonfigurować własne środowisko integration runtime do użycia odpowiednie ustawienia serwera proxy. W fazie początkowej rejestracji, można ustawić serwera proxy.

![Określ serwer proxy](media/create-self-hosted-integration-runtime/specify-proxy.png)

Własne środowisko integration runtime używa serwera proxy, aby nawiązać połączenie z usługą w chmurze. Wybierz **łącze Zmień** podczas początkowej konfiguracji. Pojawi się okno dialogowe Ustawienia serwera proxy.

![Ustaw serwer proxy](media/create-self-hosted-integration-runtime/set-http-proxy.png)

Dostępne są trzy opcje konfiguracji:

- **Nie używaj serwera proxy**: Własne środowisko integration runtime nie jawnie za pomocą dowolnego serwera proxy połączyć z usług w chmurze.
- **Użyj serwera proxy systemu**: Ustawienia serwera proxy skonfigurowanego w diahost.exe.config i diawp.exe.config korzysta z własnego środowiska integration runtime. Jeśli żaden serwer proxy jest skonfigurowany w diahost.exe.config i diawp.exe.config, własne środowisko integration runtime łączy się z usługą w chmurze bezpośrednio, bez przechodzenia przez serwer proxy.
- **Użyj niestandardowego serwera proxy**: Konfiguruj ustawienia do użycia dla własnego środowiska integration runtime, zamiast korzystać z konfiguracji w diahost.exe.config i diawp.exe.config proxy HTTP. **Adres** i **portu** są wymagane. **Nazwa użytkownika** i **hasło** są opcjonalne, w zależności od ustawienia uwierzytelniania na serwerze proxy. Wszystkie ustawienia są szyfrowane przy użyciu interfejsu DPAPI Windows na własne środowisko integration runtime i przechowywane lokalnie na komputerze.

Usługa hosta integration runtime powoduje automatyczne ponowne uruchomienie po zapisaniu zaktualizowanych ustawień serwera proxy.

Po własnego środowiska integration runtime został pomyślnie zarejestrowany, jeśli chcesz wyświetlić lub zaktualizować ustawienia serwera proxy, należy użyć Menedżera konfiguracji produktu Integration Runtime.

1. Otwórz **Menedżera konfiguracji produktu Microsoft Integration Runtime**.
2. Przejdź do karty **Ustawienia**.
3. Wybierz **zmiany** łącze w **serwer HTTP Proxy** sekcji, aby otworzyć **Ustaw serwer Proxy HTTP** okno dialogowe.
4. Wybierz opcję **Dalej**. Następnie wyświetlone ostrzeżenie, która prosi o uprawnienie do zapisywania ustawienia serwera proxy i ponownie uruchom usługę hosta integration runtime.

Można wyświetlić i zaktualizować serwer proxy HTTP za pomocą narzędzia Configuration Manager.

![Serwer proxy w widoku](media/create-self-hosted-integration-runtime/view-proxy.png)

> [!NOTE]
> Jeśli skonfigurowano serwer proxy przy użyciu metody NTLM usługa hosta integration runtime jest uruchamiany w ramach konta domeny. Jeśli zmienisz hasło do konta domeny w dalszej części, pamiętaj, aby zaktualizować ustawienia konfiguracji dla usługi, a następnie uruchom go ponownie w związku z tym. Ze względu na to wymaganie zaleca się użyć konta domeny dedykowanego serwera proxy, który nie wymaga częstej aktualizacji hasła dostępu do.

### <a name="configure-proxy-server-settings"></a>Skonfiguruj ustawienia serwera proxy

Jeśli wybierzesz **Użyj systemowego serwera proxy** ustawienia serwera proxy HTTP, własne środowisko integration runtime wykorzystuje ustawienie diahost.exe.config i diawp.exe.config serwera proxy. Jeśli żaden serwer proxy jest określona w diahost.exe.config i diawp.exe.config, własne środowisko integration runtime łączy się z usługą w chmurze bezpośrednio bez konieczności zwracania się za pośrednictwem serwera proxy. Poniższa procedura zawiera instrukcje dotyczące aktualizowania pliku diahost.exe.config:

1. W Eksploratorze plików należy utworzyć kopię bezpieczne C:\Program Files\Microsoft integracji Runtime\3.0\Shared\diahost.exe.config do tworzenia kopii zapasowej oryginalnego pliku.
2. Otwórz Notepad.exe uruchomione jako administrator, a następnie otwórz plik tekstowy C:\Program Files\Microsoft integracji Runtime\3.0\Shared\diahost.exe.config. Znajdź tag domyślny dla przestrzeni nazw system.net, jak pokazano w poniższym kodzie:

    ```xml
    <system.net>
        <defaultProxy useDefaultCredentials="true" />
    </system.net>
    ```
    Następnie można dodać szczegółów serwera proxy, jak pokazano w poniższym przykładzie:

    ```xml
    <system.net>
        <defaultProxy enabled="true">
              <proxy bypassonlocal="true" proxyaddress="http://proxy.domain.org:8888/" />
        </defaultProxy>
    </system.net>
    ```

    Dodatkowe właściwości są dozwolone wewnątrz tagu serwera proxy do określania wymaganych ustawień, takich jak `scriptLocation`. Zobacz [serwera proxy — Element (ustawienia sieci)](https://msdn.microsoft.com/library/sa91de1e.aspx) składni.

    ```xml
    <proxy autoDetect="true|false|unspecified" bypassonlocal="true|false|unspecified" proxyaddress="uriString" scriptLocation="uriString" usesystemdefault="true|false|unspecified "/>
    ```
3. Zapisz plik konfiguracyjny w oryginalnej lokalizacji. Następnie uruchom ponownie własnego środowiska integration runtime usługi hosta, która uwzględnia zmiany. 

   Aby ponownie uruchomić usługę, użyj apletu usługi w Panelu sterowania. Lub wybierz z integracji środowiska uruchomieniowego programu Configuration Manager, **Zatrzymaj usługę** przycisk, a następnie wybierz **Uruchom usługę**. 
   
   Jeśli usługa nie zostanie uruchomiona, istnieje prawdopodobieństwo, że niepoprawną składnię tagu XML została dodana w pliku konfiguracyjnym aplikacji, który był edytowany.

> [!IMPORTANT]
> Nie zapomnij zaktualizować diahost.exe.config i diawp.exe.config.

Należy również upewnić się, że Microsoft Azure znajduje się w dozwolonych w firmie. Możesz pobrać listę prawidłowych adresów IP platformy Microsoft Azure [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=41653).

### <a name="possible-symptoms-for-firewall-and-proxy-server-related-issues"></a>Możliwe objawy zapory i serwera proxy problemów związanych z serwerem
Jeśli wystąpią błędy podobne do poniższych, prawdopodobnie z powodu nieprawidłowej konfiguracji zapory lub serwera proxy serwera, który blokuje własnego środowiska integration runtime połączenie z usługą Data Factory do samodzielnego uwierzytelnienia. Aby upewnić się, czy serwer zapory i serwera proxy są prawidłowo skonfigurowane, zapoznaj się z poprzedniej sekcji.

* Podczas próby zarejestrowania własnego środowiska integration runtime, pojawi się następujący błąd: "Nie można zarejestrować ten węzeł Integration Runtime. Upewnij się, że klucz uwierzytelniania jest prawidłowy, a usługa integracji usługi hosta jest uruchomiona na tym komputerze."
* Po otwarciu Menedżera konfiguracji produktu Integration Runtime, zostanie wyświetlony stan **Disconnected** lub **łączenie**. Podczas przeglądania dzienników zdarzeń Windows, w obszarze **Podgląd zdarzeń** > **Dzienniki aplikacji i usług** > **produktu Microsoft Integration Runtime**, zostaną wyświetlone komunikaty o błędach podobny do tego:

    ```
    Unable to connect to the remote server
    A component of Integration Runtime has become unresponsive and restarts automatically. Component name: Integration Runtime (Self-hosted).
    ```

### <a name="enabling-remote-access-from-an-intranet"></a>Włączanie dostęp zdalny z intranetu  
Jeśli używasz programu PowerShell do szyfrowania poświadczeń z innego komputera (w sieci) niż zainstalowanym własnego środowiska integration runtime, aby umożliwić **dostęp zdalny z intranetu** opcji. Po uruchomieniu programu PowerShell w celu szyfrowania poświadczeń na tym samym komputerze z zainstalowanym własnego środowiska integration runtime, nie można włączyć **dostęp zdalny z intranetu**.

Należy włączyć **dostęp zdalny z intranetu** przed dodaniem innego węzła o wysokiej dostępności i skalowalności.  

Instalator Self-Hosted integration runtime (później 3.3.xxxx.x wersji), domyślnie wyłącza instalacji Self-Hosted integration runtime **dostęp zdalny z intranetu** maszynie Self-Hosted integration runtime.

Jeśli używasz zapory innych firm, możesz ręcznie otworzyć port 8060 (lub skonfigurowane przez użytkownika). Jeśli masz problem z zaporą, podczas konfigurowania własnego środowiska integration runtime, spróbuj przy użyciu następującego polecenia do zainstalowania własnego środowiska integration runtime, bez konieczności konfigurowania zapory:

```
msiexec /q /i IntegrationRuntime.msi NOFIREWALL=1
``` 

Jeśli wybierzesz nie otworzyć port 8060 maszynie Self-Hosted integration runtime, użyj mechanizmów innej niż aplikacja Ustawianie poświadczeń, aby skonfigurować poświadczenia magazynu danych. Na przykład, można użyć **New AzDataFactoryV2LinkedServiceEncryptCredential** polecenia cmdlet programu PowerShell.


## <a name="next-steps"></a>Kolejne kroki
Zobacz następujące samouczki krok po kroku dotyczące: [Samouczek: Kopiowanie danych do chmury lokalnych](tutorial-hybrid-copy-powershell.md).
