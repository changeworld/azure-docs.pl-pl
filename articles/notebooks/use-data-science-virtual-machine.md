---
title: Korzystanie z maszyn wirtualnych do nauki o danych platformy Azure
description: Dowiedz się, jak połączyć się z maszyną wirtualną do nauki o danych platformy Azure (DSVM), aby rozszerzyć moc obliczeniową dostępną do usługi Azure Notebooks Preview.
author: getroyer
manager: andneil
ms.author: getroyer
ms.topic: how-to
ms.date: 06/13/2019
ms.openlocfilehash: b4da63b7b2a6da4316215b85a09ca7420745251c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78898399"
---
# <a name="use-azure-data-science-virtual-machines"></a>Korzystanie z maszyn wirtualnych do nauki o danych platformy Azure

Domyślnie projekty są uruchamiane w warstwie **Wolne obliczenia,** która jest ograniczona do 4 GB pamięci i 1 GB danych, aby zapobiec nadużyciom. Można pominąć te ograniczenia przy użyciu innej maszyny wirtualnej, które zostały aprowidzone w ramach subskrypcji platformy Azure. W tym celu najlepszym wyborem jest maszyna wirtualna do nauki o danych platformy Azure (DSVM) przy użyciu obrazu **maszyny wirtualnej do nauki o danych dla systemu Linux (Ubuntu).** Taki dsvm jest wstępnie skonfigurowany ze wszystkim, czego potrzebujesz dla notesów platformy Azure i pojawia się automatycznie na liście rozwijanej **Uruchom** w notesach platformy Azure.

> [!Note]
> Notesy platformy Azure jest obsługiwany tylko na dsvms utworzone za pomocą obrazu Ubuntu systemu Linux. Notesy nie są obsługiwane w obrazach systemu Windows 2012, Windows 2016 ani Linux CentOS.

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

## <a name="create-a-dsvm-instance"></a>Tworzenie wystąpienia DSVM

Aby utworzyć nowe wystąpienie DSVM, postępuj zgodnie z instrukcjami [dotyczącymi Tworzenie maszyny wirtualnej do nauki o danych Ubuntu](/azure/machine-learning/data-science-virtual-machine/dsvm-ubuntu-intro). Aby uzyskać więcej informacji, w tym szczegóły dotyczące cen, zobacz [Maszyny wirtualne do nauki o danych](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/).

## <a name="connect-to-the-dsvm"></a>Łączenie się z dsvm

Po utworzeniu dsvm, wybierz listę rozwijaną **Uruchom** na pulpicie nawigacyjnym projektu notesów platformy Azure i wybierz odpowiednie wystąpienie DSVM. Lista rozwijana pokazuje wystąpienia DSVM, jeśli spełnione są następujące warunki:

- Zalogujesz się do notesów platformy Azure przy użyciu konta korzystającego z usługi Azure Active Directory (AAD), takiego jak konto firmowe.
- Twoje konto jest połączone z subskrypcją platformy Azure.
- Masz co najmniej jedną maszynę wirtualną w tej subskrypcji, z co najmniej dostępem czytnika, który używa obrazu maszyny wirtualnej nauki o danych dla systemu Linux (Ubuntu).You have one in a more virtual machines in that subscription, with at least Reader access, that uses the Data Science Virtual Machine for Linux (Ubuntu) image.)

![Wystąpienia maszyny wirtualnej nauki o danych na liście rozwijanej na pulpicie nawigacyjnym projektu](media/project-compute-tier-dsvm.png)

Po wybraniu wystąpienia DSVM notesy platformy Azure może monitować o poświadczenia określonego komputera używane podczas tworzenia maszyny wirtualnej.

> [!Important]
> Nazwa użytkownika musi być małe litery, aby używać go z JupyterHub.

Jeśli którykolwiek z warunków nie są spełnione, nadal można połączyć się z DSVM. Na liście rozwijanej wybierz opcję **Bezpośrednie obliczanie,** która monituje o nazwę (do wyświetlenia na liście), adres IP maszyny Wirtualnej i port (zazwyczaj 8000, domyślny port, do którego nasłuchuje JupyterHub) i poświadczenia maszyny Wirtualnej:

![Monitowanie o zbieranie informacji o serwerze dla opcji Obliczenia bezpośrednie](media/project-compute-tier-direct.png)

Te wartości można uzyskać ze strony DSVM w witrynie Azure portal.

## <a name="accessing-azure-notebooks-files-from-the-dsvm"></a>Uzyskiwanie dostępu do plików notesów platformy Azure z systemu DSVM

Dostęp do systemu plików jest obsługiwany dla dsvm w wersji 19.06.15 lub nowszej. Aby sprawdzić wersję, najpierw połącz się ze urządzeniem DSVM `curl -H Metadata:true "http://169.254.169.254/metadata/instance?api-version=2018-10-01"` za pośrednictwem protokołu SSH, a następnie uruchom następujące polecenie: (należy użyć dokładnego adresu IP pokazanego tutaj). Numer wersji jest wyświetlany na wyjściu dla "version".

Aby zachować parzystość ścieżek plików w warstwie **Wolne obliczenia,** można otworzyć tylko jeden projekt naraz na dsvm. Aby otworzyć nowy projekt, należy najpierw zamknąć otwarty projekt.

Gdy projekt jest uruchamiany na maszynie wirtualnej, pliki są instalowane w katalogu głównym serwera Jupyter (katalog pokazany w usłudze JupyterHub), zastępując domyślne pliki notesów platformy Azure. Po zamknięciu maszyny Wirtualnej przy użyciu przycisku **Zamykanie** w interfejsie użytkownika notesu notesy przywracają pliki domyślne.

![Przycisk Zamykanie w notesach platformy Azure](media/shutdown.png)

## <a name="create-new-dsvm-users"></a>Tworzenie nowych użytkowników DSVM

Jeśli wielu użytkowników współużytkuje dsvm, można uniknąć blokowania siebie nawzajem, tworząc i używając użytkownika DSVM dla każdego użytkownika notesu:

1. W [witrynie Azure portal](https://portal.azure.com)przejdź do maszyny wirtualnej.
1. W obszarze **Obsługa + rozwiązywanie problemów** na lewym marginesie wybierz pozycję **Resetuj hasło**.
1. Wprowadź nową **nazwę użytkownika**. Nazwa użytkownika musi być małe litery, aby używać go z JupyterHub. Wprowadź hasło. Następnie wybierz pozycję **Aktualizuj**. (Nie ma to wpływu na istniejące nazwy użytkowników).
1. Powtórz poprzedni krok dla wszystkich dodatkowych użytkowników.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o maszynach wirtualnych DSVM [na temat wprowadzenie do maszyn wirtualnych do nauki o danych platformy Azure.](/azure/machine-learning/data-science-virtual-machine/overview)
