---
title: 'Azure Portal: tworzenie wystąpienia zarządzanego SQL | Microsoft Docs'
description: Tworzenie wystąpienia zarządzanego SQL, środowiska sieciowego i udostępnionej maszyny wirtualnej klienta.
keywords: samouczek usługi sql database, tworzenie wystąpienia zarządzanego sql
services: sql-database
author: jovanpop-msft
manager: craigg
ms.service: sql-database
ms.custom: DBs & servers
ms.topic: quickstart
ms.date: 08/13/2018
ms.author: jovanpop-msft
ms.openlocfilehash: cb378c2d2773096992ef688653fd77b2625f8754
ms.sourcegitcommit: 744747d828e1ab937b0d6df358127fcf6965f8c8
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/16/2018
ms.locfileid: "42022690"
---
# <a name="create-an-azure-sql-managed-instance"></a>Tworzenie wystąpienia zarządzanego Azure SQL

Ten przewodnik Szybki Start zawiera szczegółowe instrukcje tworzenia wystąpienia zarządzanego SQL na platformie Azure. Wystąpienie zarządzane usługi Azure SQL Database to wystąpienie aparatu bazy danych programu SQL Server wdrożone w postaci platformy jako usługi (PaaS, Platform as a Service). Umożliwia ono uruchamianie i skalowanie baz danych programu SQL Server o wysokiej dostępności w chmurze platformy Azure. W tym przewodniku Szybki start pokazano, jak rozpocząć pracę od utworzenia wystąpienia zarządzanego SQL.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne](https://azure.microsoft.com/free/) konto.

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

## <a name="prepare-network-environment"></a>Przygotowywanie środowiska sieciowego

Wystąpienie zarządzane SQL to bezpieczna usługa, która znajduje się w Twojej sieci wirtualnej platformy Azure. Aby utworzyć wystąpienie zarządzane, musisz przygotować środowisko sieciowe platformy Azure, które obejmuje następujące elementy:
 - Sieć wirtualna platformy Azure, w której znajduje się wystąpienie zarządzane.
 - Podsieć należąca do sieci wirtualnej platformy Azure, w której znajdują się wystąpienia zarządzane.
 - Trasa zdefiniowana przez użytkownika umożliwiająca komunikację między wystąpieniem zarządzanym a usługami platformy Azure, które sterują i zarządzają tym wystąpieniem.

Podsieć służy do obsługi wystąpień zarządzanych. Nie można w niej tworzyć innych zasobów (na przykład maszyn wirtualnych platformy Azure). W tym przewodniku Szybki start utworzymy dwie podsieci w sieci wirtualnej platformy Azure. Wystąpienia zarządzane zostaną umieszczone w dedykowanej podsieci, a inne zasoby — w domyślnej podsieci.

1. Wdróż środowisko sieciowe platformy Azure przygotowane na potrzeby wystąpienia zarządzanego usługi Azure SQL Database, klikając poniższy przycisk:

    <a target="_blank" href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-sql-managed-instance-azure-environment%2Fazuredeploy.json" rel="noopener"> <img src="http://azuredeploy.net/deploybutton.png"> </a>

    Pojawi się formularz w witrynie Azure Portal, który pozwala skonfigurować środowisko sieciowe przed jego wdrożeniem.

2. Opcjonalnie możesz zmienić nazwy sieci wirtualnej i podsieci, a także dostosować zakresy adresów IP skojarzone z zasobami sieciowymi. Następnie naciśnij przycisk „Kup”, aby tworzyć i skonfigurować środowisko:

    ![tworzenie środowiska wystąpienia zarządzanego](./media/sql-database-managed-instance-get-started/create-mi-network-arm.png)

    > [!Note]
    > Wdrożenie usługi Azure Resource Manager utworzy w sieci wirtualnej dwie podsieci. Pierwsza z nich, o nazwie **ManagedInstances**, służy do obsługi wystąpień zarządzanych. Druga podsieć nosi nazwę **Default** i jest przeznaczona dla innych zasobów, takich jak maszyna wirtualna klienta, za pomocą której można łączyć się z wystąpieniem zarządzanym. Jeśli nie potrzebujesz dwóch podsieci, możesz usunąć podsieć domyślną, jednak wtedy nie będziesz w stanie wykonać kroku 3 opisanego w tym przewodniku Szybki start — [przygotowania maszyny klienta](#prepare-client-machine).

    > [!Note]
    > Jeśli zmienisz nazwy sieci wirtualnej i podsieci, koniecznie je zapamiętaj — będą one potrzebne w kolejnych sekcjach. W pozostałej części tego samouczka zakłada się, że utworzono sieć wirtualną o nazwie **MyNewVNet**, podsieć **ManagedInstances** dla wystąpień zarządzanych SQL oraz podsieć **Default** dla maszyn wirtualnych i innych zasobów.

## <a name="create-a-managed-instance"></a>Tworzenie wystąpienia zarządzanego

Poniższe kroki pokazują, jak utworzyć wystąpienie zarządzane po zatwierdzeniu wersji zapoznawczej.

1. W lewym górnym rogu witryny Azure Portal kliknij przycisk **Utwórz zasób**.
2. Zlokalizuj **Wystąpienie zarządzane**, a następnie wybierz pozycję **Wystąpienie zarządzane usługi Azure SQL Database (wersja zapoznawcza)**.
3. Kliknij pozycję **Utwórz**.

   ![Tworzenie wystąpienia zarządzanego](./media/sql-database-managed-instance-tutorial/managed-instance-create.png)

4. Wybierz subskrypcję i upewnij się, że warunki dotyczące wersji zapoznawczej są oznaczone jako **Zaakceptowane**.

   ![zaakceptowana wersja zapoznawcza wystąpienia zarządzanego](./media/sql-database-managed-instance-tutorial/preview-accepted.png)

5. Wypełnij formularz wystąpienia zarządzanego, podając wymagane informacje. Skorzystaj z informacji w następującej tabeli:

   | Ustawienie| Sugerowana wartość | Opis |
   | ------ | --------------- | ----------- |
   |**Nazwa wystąpienia zarządzanego**|Dowolna prawidłowa nazwa|Prawidłowe nazwy opisano w artykule [Ograniczenia i reguły nazewnictwa](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions).|
   |**Identyfikator logowania administratora wystąpienia zarządzanego**|Dowolna prawidłowa nazwa użytkownika|Prawidłowe nazwy opisano w artykule [Ograniczenia i reguły nazewnictwa](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions). Nie używaj nazwy „serveradmin”, gdyż jest ona zarezerwowana dla roli poziomu serwera.| 
   |**Hasło**|Dowolne prawidłowe hasło|Hasło musi mieć co najmniej 16 znaków i spełniać [zdefiniowane wymagania dotyczące złożoności](../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm).|
   |**Grupa zasobów**|Grupa zasobów, która została utworzona wcześniej||
   |**Lokalizacja**|Lokalizacja, która została wybrana wcześniej|Aby uzyskać informacje na temat regionów, zobacz temat [Regiony platformy Azure](https://azure.microsoft.com/regions/).|
   |**Sieć wirtualna**|Sieć wirtualna, która została utworzona wcześniej| Jeśli nie zmieniono nazw w poprzednim kroku, wybierz element **MyNewVNet/ManagedInstances**. W przeciwnym razie wybierz nazwę sieci wirtualnej i nazwę podsieci z wystąpieniem zarządzanym, które zostały wprowadzone w poprzedniej sekcji. **Nie używaj domyślnej podsieci, ponieważ nie została ona skonfigurowana pod kątem hostowania wystąpień zarządzanych**. |

   ![formularz tworzenia wystąpienia zarządzanego](./media/sql-database-managed-instance-tutorial/managed-instance-create-form.png)

6. Kliknij pozycję **Warstwa cenowa**, aby ustalić rozmiar zasobów obliczeniowych i magazynu, a także sprawdzić opcje warstw cenowych. Domyślnie wystąpienie zyskuje bezpłatnie 32 GB miejsca do magazynowania, **co może nie być wystarczające dla Twoich aplikacji**.
7. Użyj suwaków lub pól tekstowych, aby określić ilość pamięci i liczbę rdzeni wirtualnych. 
   ![wystąpienie zarządzane warstwy cenowej](./media/sql-database-managed-instance-tutorial/managed-instance-pricing-tier.png)

8. Po zakończeniu kliknij pozycję **Zastosuj**, aby zapisać wybór.  
9. Kliknij pozycję **Utwórz**, aby wdrożyć wystąpienie zarządzane.
10. Kliknij ikonę **Powiadomienia**, aby wyświetlić stan wdrożenia.
 
   ![postęp wdrażania](./media/sql-database-managed-instance-tutorial/deployment-progress.png)

11. Kliknij pozycję **Wdrażanie jest w toku**, aby otworzyć okno wystąpienia zarządzanego i dokładniej monitorować postęp wdrażania.
 
   ![postęp wdrażania 2](./media/sql-database-managed-instance-tutorial/managed-instance.png)

Podczas wdrażania możesz przejść do następnej procedury.

> [!IMPORTANT]
> W przypadku pierwszego wystąpienia w podsieci czas wdrożenia jest zazwyczaj znacznie dłuższy niż w przypadku kolejnych wystąpień — czasami zakończenie procesu zajmuje ponad 24 godziny. Nie anuluj operacji wdrażania, ponieważ proces trwa dłużej niż oczekiwano. Ten długi czas wdrożenia pierwszego wystąpienia jest sytuacją tymczasową. Wkrótce po rozpoczęciu publicznej wersji zapoznawczej należy oczekiwać znacznej redukcji czasu wdrożenia. Utworzenie drugiego wystąpienia zarządzanego w podsieci potrwa kilka minut.

## <a name="prepare-client-machine"></a>Przygotowywanie maszyny klienta

Ponieważ wystąpienie zarządzane SQL znajduje się w prywatnej sieci wirtualnej, musisz utworzyć maszynę wirtualną platformy Azure z zainstalowanym narzędziem klienckim, takim jak SQL Server Management Studio lub SQL Operations Studio, umożliwiającym łączenie się z wystąpieniem zarządzanym i wykonywanie zapytań.

> [!Note]
> Zamiast klienckiej maszyny wirtualnej platformy Azure możesz skonfigurować [połączenie punkt-lokacja](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md) i łączyć się z wystąpieniem zarządzanym za pomocą komputera lokalnego.

Najprostszy sposób utworzenia maszyny wirtualnej klienta ze wszystkimi niezbędnymi narzędziami polega na użyciu szablonów usługi Azure Resource Manager.

1. Kliknij poniższy przycisk (upewnij się, że zalogowano się w witrynie Azure Portal w innej karcie przeglądarki):

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjovanpop-msft%2Fazure-quickstart-templates%2Fsql-win-vm-w-tools%2F201-vm-win-vnet-sql-tools%2Fazuredeploy.json" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"/></a>

2. W otwartym formularzu wprowadź nazwę maszyny wirtualnej, nazwę użytkownika administratora i hasło, które będą używane do łączenia się z maszyną wirtualną.

    ![tworzenie maszyny wirtualnej klienta](./media/sql-database-managed-instance-get-started/create-client-sql-vm.png)

    Jeśli wcześniej nie zmieniono nazwy sieci wirtualnej ani nazwy domyślnej podsieci, nie trzeba zmieniać dwóch ostatnich parametrów. W przeciwnym razie należy zmienić te wartości na wartości wprowadzone podczas konfigurowania środowiska sieciowego.

3. Po kliknięciu przycisku „Kup” maszyna wirtualna platformy Azure zostanie wdrożona w przygotowanej sieci.

4. Połącz się z maszyną wirtualną za pomocą funkcji Podłączanie pulpitu zdalnego i znajdź program SQL Server Management Studio lub SQL Operation Studio — są one automatycznie instalowane na maszynie wirtualnej.

5. Otwórz program SSMS i wprowadź **nazwę hosta** wystąpienia zarządzanego w polu **Nazwa serwera**, wybierz opcję **Uwierzytelnianie programu SQL Server**, podaj login i hasło w oknie dialogowym **Połącz z serwerem**, a następnie kliknij opcję **Połącz**.

    ![nawiązywanie połączenia w programie ssms](./media/sql-database-managed-instance-tutorial/ssms-connect.png)  

Po nawiązaniu połączenia możesz wyświetlić systemowe bazy danych oraz bazy danych użytkownika w węźle Bazy danych, a także różne obiekty w węzłach Zabezpieczenia, Obiekty serwera, Replikacja, Zarządzanie, Agent programu SQL Server oraz Profiler systemu XEvent.

## <a name="next-steps"></a>Następne kroki

 - [Connect your applications to Managed Instance (Łączenie aplikacji z wystąpieniem zarządzanym)](sql-database-managed-instance-connect-app.md)
 - [Migrate your databases from on-premises to Managed Instance (Migrowanie baz danych ze środowiska lokalnego do wystąpienia zarządzanego)](sql-database-managed-instance-migrate.md)


