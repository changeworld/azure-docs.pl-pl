---
title: Jak przeprowadzić uaktualnienie w miejscu wersji programu SQL Server na maszynie Wirtualnej platformy Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak zmienić wersję maszyny Wirtualnej programu SQL Server na platformie Azure.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: jroth
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/26/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 05d2170fe9e6055179bf49d803d4739ddc05be89
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/05/2019
ms.locfileid: "67607548"
---
# <a name="how-to-perform-an-in-place-upgrade-of-sql-server-edition-on-azure-vm"></a>Jak przeprowadzić uaktualnienie w miejscu wersji programu SQL Server na maszynie Wirtualnej platformy Azure

W tym artykule opisano, jak zmienić wersję programu SQL Server dla istniejącego serwera SQL na maszynie wirtualnej Windows na platformie Azure. 

Wersja programu SQL Server jest określany przez klucz produktu i jest określony za pomocą procesu instalacji. Co mówią, wersja [funkcji](/sql/sql-server/editions-and-components-of-sql-server-2017) są dostępne w ramach produktu SQL Server. Można zmienić wersji programu SQL Server przy użyciu nośnika instalacyjnego i albo obniżenie poziomu, obniżenia kosztów lub wykonać uaktualnienie do większej liczby funkcji.

Jeśli zaktualizowano wersji programu SQL Server po zarejestrowaniu dostawcy zasobów maszyny Wirtualnej SQL przy użyciu nośnika instalacyjnego można zaktualizować platformy Azure rozliczeń w związku z tym należy powinien wartość właściwości SQL Server Edition zasobu maszyny Wirtualnej SQL w następujący sposób:

1. Zaloguj się do [Azure Portal](https://portal.azure.com). 
1. Przejdź do zasobu maszyny wirtualnej programu SQL Server. 
1. W obszarze **ustawienia**, wybierz opcję **Konfiguruj** a następnie wybierz odpowiednią wersję programu SQL Server z listy rozwijanej w obszarze **wersji**. 

   ![Zmiana wersji metadanych](media/virtual-machines-windows-sql-change-edition/edition-change-in-portal.png)

1. Przejrzyj ostrzeżenie z informacją, że należy najpierw zmienić wersji programu SQL Server i właściwości wersji musi odpowiadać wersji programu SQL Server. 
1. Wybierz **Zastosuj** Aby zastosować zmiany metadanych edition. 


## <a name="prerequisites"></a>Wymagania wstępne

Aby zrobić to zmiana w miejscu programu SQL Server w wersji, potrzebne następujące elementy: 

- [Subskrypcji platformy Azure](https://azure.microsoft.com/free/).
- Windows [maszyny Wirtualnej programu SQL Server](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision) zarejestrowane w usłudze [dostawcy zasobów maszyny Wirtualnej SQL](virtual-machines-windows-sql-register-with-resource-provider.md).
- Instalator z nośnika z odpowiednią wersję programu SQL Server. Klienci, którzy mają [pakietu software assurance](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default) można uzyskać ich nośnika instalacyjnego z [Centrum licencjonowania zbiorowego](https://www.microsoft.com/Licensing/servicecenter/default.aspx). Klienci nie muszą software assurance można użyć nośnika instalacyjnego z obrazu maszyny Wirtualnej programu SQL Server portalu marketplace, który ma ich żądaną wersję.


## <a name="upgrade-edition"></a>Uaktualnienia wersji

  > [!WARNING]
  > - **Uaktualnianie wersji programu SQL Server powoduje ponowne uruchomienie usługi programu SQL Server, jak również wszelkie skojarzone usługi, takie jak usługi Analysis Services i usługami R Services.** 

Aby uaktualnić wersję programu SQL Server, uzyskiwanie nośnika instalacyjnego programu SQL Server dla żądanej wersji programu SQL Server, a następnie wykonaj następujące czynności:

1. Uruchom Setup.exe z nośnika instalacyjnego programu SQL Server. 
1. Przejdź do **konserwacji** i wybierz polecenie **uaktualnienie wersji** opcji. 

   ![Uaktualnianie wersji programu SQL Server](media/virtual-machines-windows-sql-change-edition/edition-upgrade.png)

1. Wybierz **dalej** aż **gotowe do uaktualnienia wersji** strony, a następnie wybierz pozycję **uaktualnienia**. Okno Ustawienia mogą wykraczać przez kilka minut, podczas zmiany trwa efekt, a następnie zostanie wyświetlony **Complete** stronę potwierdzenia, że uaktualnienie wersji została zakończona. 

Po uaktualnieniu wersji programu SQL Server, należy zmodyfikować właściwość "Edition" z maszyny wirtualnej Sql w witrynie Azure portal, jak pokazano powyżej. Spowoduje to zaktualizowanie metadanych i rozliczenia związane z tą maszyną Wirtualną.

## <a name="downgrade-edition"></a>Zmiany wersji na starszą wersję

  > [!WARNING]
  > - **Zmiana wersji na starszą wersję programu SQL Server wymaga całkowicie odinstalowywania programu SQL Server, co może pociągnąć za sobą dodatkowe przestój**. 


Zmiany na starszą wersję programu SQL Server, należy całkowicie odinstalować program SQL Server, a następnie zainstaluj go ponownie przy użyciu nośnika instalacyjnego żądanej wersji. 

Wersja programu SQL Server mogą obniżyć wersję, wykonując następujące czynności:

1. Utwórz kopię zapasową wszystkich baz danych, w tym systemowych baz danych. 
1. Przenieś systemowych baz danych (master, model i msdb) do nowej lokalizacji. 
1. Całkowicie Odinstaluj program SQL Server i wszystkich powiązanych z nim usług. 
1. Uruchom ponownie maszynę wirtualną. 
1. Zainstaluj program SQL Server przy użyciu nośnika z odpowiednią wersję programu SQL Server.
1. Zainstaluj najnowsze dodatki service pack i aktualizacjami zbiorczymi.  
1. Zastąp nowych baz danych systemu, które zostały utworzone podczas instalacji przy użyciu bazy danych systemu, które zostały wcześniej przeniesione do innej lokalizacji. 

Po wersji programu SQL Server jest obniżona, należy zmodyfikować właściwość "Edition" maszyny wirtualnej SQL w witrynie Azure portal, jak pokazano powyżej. Spowoduje to zaktualizowanie metadanych i rozliczenia związane z tą maszyną Wirtualną.

## <a name="remarks"></a>Uwagi

 - Właściwość edition dla maszyny Wirtualnej programu SQL Server musi odpowiadać wersji programu SQL Server zainstalowane do maszyny wirtualnej dla wszystkich maszyn wirtualnych SQL w tym typów licencji zarówno w PAYG, jak i w ramach opcji BYOL.
 - Jeśli usuniesz zasób maszynę Wirtualną programu SQL Server, będzie wrócisz do ustawienia ustaloną wersji obrazu.
  - Możliwość zmiany wersji jest funkcją dostawcy zasobów maszyny Wirtualnej SQL. Wdrażanie obrazu z witryny marketplace w witrynie Azure portal automatycznie rejestruje maszynę Wirtualną programu SQL Server za pomocą dostawcy zasobów. Jednak klienci, którzy własnym zainstalowany program SQL Server należy ręcznie [zarejestrować ich SQL Server VM](virtual-machines-windows-sql-register-with-resource-provider.md).
- Dodanie maszyny Wirtualnej programu SQL Server do zestawu dostępności, wymaga ponownego tworzenia maszyny Wirtualnej. Jako takie, wszystkie maszyny wirtualne dodane do dostępności zestawu przechodzi do wersji domyślnej, a wersja będzie konieczne ich ponownego zmodyfikowania.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji zobacz następujące artykuły: 

* [Omówienie programu SQL Server na maszynie Wirtualnej Windows](virtual-machines-windows-sql-server-iaas-overview.md)
* [SQL Server w usłudze Windows maszyny Wirtualnej — często zadawane pytania](virtual-machines-windows-sql-server-iaas-faq.md)
* [SQL Server na maszynie Wirtualnej Windows wskazówki dotyczące cen](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Program SQL Server w wersji Windows VM](virtual-machines-windows-sql-server-iaas-release-notes.md)


