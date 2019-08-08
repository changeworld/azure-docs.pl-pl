---
title: Wykonaj uaktualnienie w miejscu do wersji SQL Serverej na maszynie wirtualnej platformy Azure | Microsoft Docs
description: Dowiedz się, jak zmienić wersję SQL Server maszyny wirtualnej na platformie Azure.
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
ms.openlocfilehash: 04e447b7d8da1c8769239aee7650fe3bc5585590
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/08/2019
ms.locfileid: "68855231"
---
# <a name="perform-an-in-place-upgrade-of-a-sql-server-edition-on-an-azure-vm"></a>Wykonaj uaktualnienie w miejscu dla wersji SQL Serverej na maszynie wirtualnej platformy Azure

W tym artykule opisano sposób zmiany wersji SQL Server na maszynie wirtualnej z systemem Windows na platformie Azure. 

Wersja SQL Server jest określana przez klucz produktu i jest określona w procesie instalacji. Wersja określa, jakie [funkcje](/sql/sql-server/editions-and-components-of-sql-server-2017) są dostępne w produkcie SQL Server. Wersję SQL Server można zmienić przy użyciu nośnika instalacyjnego, a obniżenie poziomu obniża koszty lub uaktualnienie, aby umożliwić obsługę większej liczby funkcji.

W przypadku zaktualizowania wersji SQL Server przy użyciu nośnika instalacyjnego po zarejestrowaniu się u dostawcy zasobów maszyny wirtualnej SQL, aby odpowiednio zaktualizować rozliczenia systemu Azure, należy ustawić właściwość SQL Server Edition zasobu maszyny wirtualnej SQL w następujący sposób:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). 
1. Przejdź do zasobu maszyny wirtualnej SQL Server. 
1. W obszarze **Ustawienia**wybierz pozycję **Konfiguruj**. Następnie wybierz żądaną wersję SQL Server z listy rozwijanej w obszarze **Edycja**. 

   ![Metadane zmiany wersji](media/virtual-machines-windows-sql-change-edition/edition-change-in-portal.png)

1. Przejrzyj ostrzeżenie z informacją, że musisz najpierw zmienić wersję SQL Server i że właściwość Edition musi być zgodna z wersją SQL Server. 
1. Wybierz pozycję **Zastosuj** , aby zastosować zmiany metadanych wersji. 


## <a name="prerequisites"></a>Wymagania wstępne

Aby przeprowadzić zmianę w miejscu wersji SQL Server, potrzebne są następujące elementy: 

- [Subskrypcji platformy Azure](https://azure.microsoft.com/free/).
- [SQL Server maszynę wirtualną w systemie Windows](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision) zarejestrowana przy użyciu [dostawcy zasobów maszyny wirtualnej SQL](virtual-machines-windows-sql-register-with-resource-provider.md).
- Skonfiguruj nośnik z odpowiednią wersją SQL Server. Klienci posiadający [program Software Assurance](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default) mogą uzyskać nośnik instalacyjny z [centrum licencjonowania zbiorowego](https://www.microsoft.com/Licensing/servicecenter/default.aspx). Klienci, którzy nie mają programu Software Assurance, mogą korzystać z nośnika instalacyjnego z witryny Azure Marketplace SQL Server obrazu maszyny wirtualnej z odpowiednią wersją.


## <a name="upgrade-an-edition"></a>Uaktualnianie wersji

> [!WARNING]
> Uaktualnienie wersji SQL Server spowoduje ponowne uruchomienie usługi dla SQL Server wraz ze wszystkimi skojarzonymi usługami, takimi jak Analysis Services i R Services. 

Aby uaktualnić SQL Server, uzyskaj nośnik instalacyjny SQL Server dla żądanej wersji SQL Server, a następnie wykonaj następujące czynności:

1. Otwórz plik Setup. exe z nośnika instalacyjnego programu SQL Server. 
1. Przejdź do pozycji **konserwacja** i wybierz opcję **uaktualnienia wersji** . 

   ![Wybór na potrzeby uaktualnienia wersji SQL Server](media/virtual-machines-windows-sql-change-edition/edition-upgrade.png)

1. Wybierz pozycję **dalej** do momentu uzyskania dostępu **do strony gotowy do uaktualnienia wersji** , a następnie wybierz pozycję **Uaktualnij**. Okno instalatora może przestać odpowiadać przez kilka minut, gdy zmiana zacznie obowiązywać. **Pełna** Strona potwierdzi, że uaktualnienie wersji zostało zakończone. 

Po uaktualnieniu wersji SQL Server należy zmodyfikować właściwość wersja SQL Server maszyny wirtualnej w Azure Portal jak pokazano wcześniej. Spowoduje to zaktualizowanie metadanych i rozliczeń skojarzonych z tą maszyną wirtualną.

## <a name="downgrade-an-edition"></a>Starsza wersja

Aby można było obniżyć wersję SQL Server, należy całkowicie odinstalować SQL Server i ponownie ją zainstalować przy użyciu nośnika instalacyjnego wymagane wydanie.

> [!WARNING]
> Odinstalowanie SQL Server może pociągnąć za sobą dodatkowy Przestój. 

Wersję SQL Server można obniżyć, wykonując następujące czynności:

1. Utwórz kopię zapasową wszystkich baz danych, w tym systemowych baz danych. 
1. Przenieś systemowe bazy danych (Master, model i msdb) do nowej lokalizacji. 
1. Całkowicie Odinstaluj SQL Server i wszystkie skojarzone usługi. 
1. Uruchom ponownie maszynę wirtualną. 
1. Zainstaluj SQL Server przy użyciu nośnika z odpowiednią wersją SQL Server.
1. Zainstaluj najnowsze dodatki Service Pack i aktualizacje zbiorcze.  
1. Zastąp nowe systemowe bazy danych, które zostały utworzone podczas instalacji z systemowymi bazami danych, które zostały wcześniej przeniesione do innej lokalizacji. 

Po obniżeniu wersji SQL Server należy zmodyfikować właściwość wersja SQL Server maszyny wirtualnej w Azure Portal jak pokazano wcześniej. Spowoduje to zaktualizowanie metadanych i rozliczeń skojarzonych z tą maszyną wirtualną.

## <a name="remarks"></a>Uwagi

- Właściwość Edition maszyny wirtualnej SQL Server musi być zgodna z wersją wystąpienia SQL Server zainstalowaną dla wszystkich SQL Server maszyn wirtualnych, w tym z opcją płatność zgodnie z rzeczywistym użyciem i w ramach licencji na własne typy licencji.
- W przypadku porzucenia SQL Server zasobów maszyny wirtualnej powrócisz do ustawienia kodowanej wersji obrazu.
- Możliwość zmiany wersji to funkcja dostawcy zasobów maszyny wirtualnej SQL. Wdrożenie obrazu portalu Azure Marketplace za pomocą Azure Portal powoduje automatyczne zarejestrowanie SQL Serverj maszyny wirtualnej przy użyciu dostawcy zasobów. Jednak klienci, którzy samodzielnie instalują SQL Server, będą musieli ręcznie [zarejestrować swoją SQL Serverą maszynę wirtualną](virtual-machines-windows-sql-register-with-resource-provider.md).
- Dodanie maszyny wirtualnej SQL Server do zestawu dostępności wymaga ponownego utworzenia maszyny wirtualnej. Wszystkie maszyny wirtualne dodane do zestawu dostępności pozostaną z powrotem do wersji domyślnej, a wydanie należy ponownie zmodyfikować.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji zobacz następujące artykuły: 

* [Omówienie SQL Server na maszynie wirtualnej z systemem Windows](virtual-machines-windows-sql-server-iaas-overview.md)
* [Często zadawane pytania dotyczące SQL Server na maszynie wirtualnej z systemem Windows](virtual-machines-windows-sql-server-iaas-faq.md)
* [Wskazówki dotyczące cen dla SQL Server na maszynie wirtualnej z systemem Windows](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Informacje o wersji SQL Server na maszynie wirtualnej z systemem Windows](virtual-machines-windows-sql-server-iaas-release-notes.md)


