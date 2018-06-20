---
title: Uściślij grupy oceny pomocą mapowania grup zależności w migracji Azure | Dokumentacja firmy Microsoft
description: Opisuje sposób uściślić oceny w usłudze Azure migracji za pomocą mapowania grup zależności.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 06/19/2018
ms.author: raynew
ms.openlocfilehash: a91543dcea1fb9fb6a33b5a7bc3892a2bac7b241
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/19/2018
ms.locfileid: "36215787"
---
# <a name="refine-a-group-using-group-dependency-mapping"></a>Dostosuj grupę za pomocą mapowania grup zależności

W tym artykule opisano sposób zakres grupy wizualizowanie zależności wszystkie maszyny w grupie. Aby uściślić członkostwa do istniejącej grupy, krzyżowe sprawdzanie zależnościami grupy, przed uruchomieniem ocenę zwykle Użyj tej metody. Uściślenie grupę za pomocą wizualizacji zależności można skutecznie Zaplanuj migrację Azure.You pomocy może odnaleźć wszystkie współzależne systemy, które wymagają migracji ze sobą. Pomaga zapewnić, że nic pozostawione i awarie niespodziewanego występuje, gdy w przypadku migracji do usługi Azure. 


> [!NOTE]
> Grupy, dla których mają być wyświetlane zależności nie powinny zawierać więcej niż 10 maszyn. Jeśli masz więcej niż 10 maszyn w grupie, ale zalecamy podziel go na mniejsze grupy skorzystać z funkcji wizualizacji zależności.


# <a name="prepare-the-group-for-dependency-visualization"></a>Przygotowanie grupy do wizualizacji zależności
Aby wyświetlić zależności grupy, należy pobrać i zainstalować agentów na każdej maszynie lokalnej, która jest częścią grupy. Ponadto, jeśli masz maszyny bez łączności z Internetem, musisz pobrać i zainstalować [bramy OMS](../log-analytics/log-analytics-oms-gateway.md) na nich.

### <a name="download-and-install-the-vm-agents"></a>Pobieranie i instalowanie agentów maszyny wirtualnej
1. W **omówienie**, kliknij przycisk **Zarządzaj** > **grup**, przejdź do wymaganej grupy.
2. Na liście komputerów w **Dependency agent** kolumny, kliknij przycisk **wymaga instalacji** Aby uzyskać instrukcje dotyczące sposobu pobierania i instalacji agentów.
3. Na **zależności** strony, Pobierz i zainstaluj program Microsoft Monitoring Agent (MMA) i agenta zależności na każdej maszynie Wirtualnej, która jest częścią grupy.
4. Skopiuj identyfikator i klucz obszaru roboczego. Należy je po zainstalowaniu MMA na komputerach lokalnych.

### <a name="install-the-mma"></a>Instalowanie programu MMA

Aby zainstalować agenta na komputerze z systemem Windows:

1. Kliknij dwukrotnie pobranego agenta.
2. Na **stronie powitalnej** kliknij przycisk **Dalej**. Na stronie **Postanowienia licencyjne** kliknij przycisk **Zgadzam się**, aby zaakceptować warunki licencji.
3. W **Folder docelowy**, Zachowaj lub zmienić domyślny folder instalacji > **dalej**. 
4. W **opcje instalacji agenta**, wybierz pozycję **Azure Log Analytics** > **dalej**. 
5. Kliknij przycisk **Dodaj** Aby dodać nowy obszar roboczy analizy dzienników. Wklej identyfikator i klucz, który został skopiowany z portalu. Kliknij przycisk **Dalej**.


Aby zainstalować agenta na komputerze z systemem Linux:

1. Transfer odpowiedniego pakietu (x86 lub x64) na komputerze systemu Linux przy użyciu scp/sftp.
2. Instalowanie pakietu przy użyciu argumentu--instalacji.

    ```sudo sh ./omsagent-<version>.universal.x64.sh --install -w <workspace id> -s <workspace key>```


### <a name="install-the-dependency-agent"></a>Instalowanie agenta zależności
1. Aby zainstalować agenta zależności na komputerze z systemem Windows, kliknij dwukrotnie plik konfiguracji i wykonaj instrukcje kreatora.
2. Aby zainstalować agenta zależności na komputerze z systemem Linux, zainstalować jako główny przy użyciu następującego polecenia:

    ```sh InstallDependencyAgent-Linux64.bin```

[Dowiedz się więcej](../monitoring/monitoring-service-map-configure.md#supported-operating-systems) o systemach operacyjnych obsługiwanych przez agenta zależności. 

## <a name="refine-the-group-based-on-dependency-visualization"></a>Dostosuj grupy oparte na wizualizację zależności
Agenci zainstalowani na wszystkich komputerach grupy można zwizualizować zależności grupy i dostosować go, wykonując następujące czynności.

1. W systemie Azure migracji projektu, w obszarze **Zarządzaj**, kliknij przycisk **grup**i wybierz grupę.
2. Na stronie grupy kliknij **zależności widoku**, aby otworzyć mapę zależności grupy.
3. Zależności mapy dla grupy zawiera następujące informacje:
    - Przychodzące (klienci) i połączenia wychodzące (serwery) TCP do/z wszystkich maszyn, które należą do grupy
        - Zależne maszyny, które nie jest zainstalowany agent MMA i zależności są pogrupowane według numerów portów
        - Maszyny dependenct, które mają MMA i z zainstalowanym agentem zależności są wyświetlane jako osobne pola 
    - Procesów uruchomionych na maszynie, możesz rozszerzyć każde pole maszyny do wyświetlania procesów
    - Właściwości, takie jak pełni kwalifikowana nazwa domeny, System operacyjny, itp. adres MAC każdego komputera, możesz kliknąć każde pole maszyny, aby wyświetlić te szczegóły

     ![Wyświetlanie zależności grupowych](./media/how-to-create-group-dependencies/view-group-dependencies.png)

3. Aby wyświetlić bardziej szczegółowe zależności, kliknij zakres czasu, aby go zmodyfikować. Domyślnie zakres jest godzinę. Możesz zmodyfikować zakres czasu lub określ rozpoczęcia oraz datę zakończenia oraz czas trwania.
4. Sprawdź zależne maszyny, procesów uruchomionych w każdej maszyny i identyfikacji komputerów, które powinny być dodane lub usunięte z grupy.
5. Użyj Ctrl + kliknięcie, aby wybrać maszyn na mapie, aby dodać lub usunąć je z grupy.
    - Można dodawać tylko te komputery, które zostały odnalezione.
    - Dodawanie i usuwanie urządzenia z grupy unieważnia poza ocen dla niego.
    - Po zmodyfikowaniu grupy, można opcjonalnie utworzyć nowy oceny.
5. Kliknij przycisk **OK** można zapisać grupy.

    ![Dodawanie lub usuwanie komputerów](./media/how-to-create-group-dependencies/add-remove.png)

Jeśli chcesz sprawdzić zależności określonym komputerze, na którym zostanie wyświetlona na mapie zależności grupy [skonfigurowanie mapowania zależności maszyny](how-to-create-group-machine-dependencies.md).


## <a name="next-steps"></a>Kolejne kroki

[Dowiedz się więcej](concepts-assessment-calculation.md) na temat sposobu obliczania ocen.
