---
title: Użyj maszyn wirtualnych do nauki o danych platformy Azure
description: Podłącz do usługi Azure Data Science maszyny wirtualnej (dsvm dystrybucji) do rozszerzania dostępne mocy obliczeniowej do notesów usługi Azure.
services: app-service
documentationcenter: ''
author: getroyer
manager: andneil
ms.assetid: 0ccc2529-e17f-4221-b7c7-9496d6a731cc
ms.service: azure-notebooks
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/13/2019
ms.author: getroyer
ms.openlocfilehash: fe9886429a5e894f40c04b1f65094e412c1dc9e2
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/28/2019
ms.locfileid: "67441201"
---
# <a name="use-azure-data-science-virtual-machines"></a>Użyj maszyn wirtualnych do nauki o danych platformy Azure

Domyślnie projekty są uruchamiane **bezpłatne obliczenia** warstwę, która jest ograniczony do 4 GB pamięci i 1 GB danych, aby zapobiec nadużyciu. Ograniczenia te można pominąć przy użyciu innej maszyny wirtualnej, które zostały aprowizowane w subskrypcji platformy Azure. W tym celu najlepszym wyborem jest usługi Azure Data Science Virtual Machine (dsvm dystrybucji) przy użyciu **maszyna wirtualna do nauki o danych dla systemu Linux (Ubuntu)** obrazu. Takie DSVM ma wstępnie skonfigurowany wszystkie potrzebne dla notesów usługi Azure pojawia się automatycznie w **Uruchom** listy rozwijanej w notesach platformy Azure.

> [!Note]
> Notesy platformy Azure jest obsługiwana tylko na maszyny utworzone za pomocą na obraz systemu Linux Ubuntu. Notesy nie są obsługiwane w obrazach Windows 2012, Windows 2016 lub Linux CentOS.

## <a name="create-a-dsvm-instance"></a>Utwórz wystąpienie maszyny wirtualnej DSVM

Aby utworzyć nowe wystąpienie maszyny wirtualnej DSVM, postępuj zgodnie z instrukcjami [tworzenia maszyny Wirtualnej do nauki o danych Ubuntu](/azure/machine-learning/data-science-virtual-machine/dsvm-ubuntu-intro). Aby uzyskać więcej informacji, w tym szczegóły cennika, zobacz [maszyn wirtualnych do nauki o danych](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/).

## <a name="connect-to-the-dsvm"></a>Łączenie do maszyny DSVM

Po utworzeniu maszyny DSVM, wybierz **Uruchom** listy rozwijanej w notesach Azure projektu pulpitu nawigacyjnego, a następnie wybierz odpowiednie wystąpienie maszyny wirtualnej DSVM. Listy rozwijanej pokazuje wystąpień maszyny wirtualnej DSVM, jeśli są spełnione następujące warunki:

- Po zarejestrowaniu się w notesach Azure przy użyciu konta, który używa usługi Azure Active Directory (AAD), takie jak konto firmy.
- Twoje konto jest połączone z subskrypcją platformy Azure.
- Masz co najmniej jednej maszyny wirtualnej w tej subskrypcji z co najmniej dostęp czytnika, korzystającą z maszyny wirtualnej do nauki o danych dla obrazów systemu Linux (Ubuntu).)

![Wystąpienia maszyny wirtualnej do nauki o danych na liście rozwijanej na pulpicie nawigacyjnym projekt](media/project-compute-tier-dsvm.png)

Po wybraniu wystąpienia maszyny wirtualnej DSVM, notesy platformy Azure może monit o poświadczenia określonej maszyny używane podczas tworzenia maszyny Wirtualnej.

Jeśli którykolwiek z warunków nie są spełnione, nadal możesz połączyć do maszyny DSVM. Z listy rozwijanej wybierz **bezpośrednie obliczenia** opcja, która wyświetli monit o podanie nazwy (w celu wyświetlenia na liście), adres IP maszyny Wirtualnej i portu (zazwyczaj 8000, domyślny port, na którym nasłuchuje JupyterHub) i poświadczenia maszyny Wirtualnej:

![Wiersz do zbierania informacji z opcji usługi obliczenia bezpośrednie serwera](media/project-compute-tier-direct.png)

Te wartości można uzyskać ze strony DSVM w witrynie Azure portal.

## <a name="accessing-azure-notebooks-files-from-the-dsvm"></a>Dostęp do notesów usługi Azure plików z maszyny DSVM

Dostęp do systemu plików jest obsługiwana dla maszyny wirtualnej DSVM wersji 19.06.15 lub nowszym. Aby sprawdzić wersję, najpierw nawiązać połączenie z maszyny wirtualnej DSVM za pośrednictwem protokołu SSH, a następnie uruchom następujące polecenie: `curl -H Metadata:true "http://169.254.169.254/metadata/instance?api-version=2018-10-01"` (należy użyć dokładny adres IP, pokazano poniżej). Numer wersji jest wyświetlany w danych wyjściowych dla "wersja".

Aby zachować parzystości ścieżki do plików za pomocą **bezpłatne obliczeń** warstwy, będą mogli otwierać tylko jeden projekt naraz w nauki. Aby otworzyć nowy projekt, należy najpierw zamknąć otwartym projekcie.

Gdy projekt zostanie uruchomiony na maszynie Wirtualnej, pliki są instalowane w katalogu głównym serwera Jupyter (katalog objętego JupyterHub), zastępując domyślne pliki notesy platformy Azure. Podczas zamykania maszyny Wirtualnej przy użyciu **zamykania** przycisku w notesie interfejsu użytkownika, notesów usługi Azure przywraca domyślne pliki.

![Przycisk zamykania w notesach platformy Azure](media/shutdown.png)

## <a name="create-new-dsvm-users"></a>Utwórz nowych użytkowników do nauki

Jeśli wielu użytkowników współużytkuje nauki, można uniknąć, blokowanie wzajemnie poprzez tworzenie i używanie użytkownika maszyny wirtualnej DSVM, dla każdego użytkownika Notes:

1. Na [witryny Azure Portal](https://portal.azure.com), przejdź do maszyny wirtualnej.
1. W obszarze **pomoc techniczna i rozwiązywanie problemów z** na lewym marginesie, wybierz **Resetuj hasło**.
1. Wprowadź nową nazwę użytkownika i hasło, a następnie wybierz pozycję **aktualizacji**. (Istniejącej nazwy użytkowników nie są zagrożone.)
1. Powtórz poprzedni krok dla wszystkich dodatkowych użytkowników.

## <a name="next-steps"></a>Kolejne kroki

Więcej informacji na temat maszyny na [wprowadzenie do usługi Azure Data maszyny wirtualne do analizy](/azure/machine-learning/data-science-virtual-machine/overview).
