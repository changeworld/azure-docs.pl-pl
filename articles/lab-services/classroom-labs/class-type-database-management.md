---
title: Konfigurowanie laboratorium do nauczania zarządzania bazami danych dla relacyjnych baz danych | Dokumenty firmy Microsoft
description: Dowiedz się, jak skonfigurować laboratorium, aby uczyć zarządzania relacyjnych baz danych.
services: lab-services
documentationcenter: na
author: emaher
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/19/2019
ms.author: enewman
ms.openlocfilehash: 4c375487b30595251753021033c98cf0ca1e8dd7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77469922"
---
# <a name="set-up-a-lab-to-teach-database-management-for-relational-databases"></a>Konfigurowanie laboratorium do nauczania zarządzania bazami danych dla relacyjnych baz danych

W tym artykule opisano sposób konfigurowania laboratorium dla klasy zarządzania podstawowymi bazami danych w usłudze Azure Lab Services. Koncepcje baz danych są jednym z kursów wprowadzających prowadzonych w większości wydziałów informatyki w college'u. Ustrukturyzowany język zapytań (SQL) jest standardem międzynarodowym. SQL jest standardowym językiem do zarządzania bazami danych relacji, w tym dodawanie, uzyskiwanie dostępu i zarządzanie zawartością w bazie danych.  Jest to najbardziej znany ze swojego szybkiego przetwarzania, sprawdzonej niezawodności, łatwość, i elastyczność użytkowania.

W tym artykule pokażemy, jak skonfigurować szablon maszyny wirtualnej w laboratorium za pomocą serwera bazy danych MySQL i serwera SQL Server 2019.  [MySQL](https://www.mysql.com/) to swobodnie dostępny system zarządzania relacyjnej bazy danych typu open source (RDBMS).  [SQL Server 2019](https://www.microsoft.com/sql-server/sql-server-2019) to najnowsza wersja rdbms firmy Microsoft.

## <a name="lab-configuration"></a>Konfiguracja laboratorium

Aby skonfigurować to laboratorium, potrzebujesz subskrypcji platformy Azure i konta laboratorium, aby rozpocząć. Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/) przed rozpoczęciem. Po uzyskaniu subskrypcji platformy Azure możesz utworzyć nowe konto laboratorium w usłudze Azure Lab Services. Aby uzyskać więcej informacji na temat tworzenia nowego konta laboratorium, zobacz [Samouczek konfigurowania konta laboratorium](tutorial-setup-lab-account.md).  Można również użyć istniejącego konta laboratorium.

### <a name="lab-account-settings"></a>Ustawienia konta laboratorium

Włącz ustawienia opisane w poniższej tabeli dla konta laboratorium. Aby uzyskać więcej informacji na temat włączania obrazów portalu Marketplace, zobacz [Określanie obrazów portalu Marketplace dostępnych dla twórców laboratoriów.](https://docs.microsoft.com/azure/lab-services/classroom-labs/specify-marketplace-images)

| Ustawienie konta laboratorium | Instrukcje |
| ------------------- | ------------ |
|Obraz portalu Marketplace| Włącz obraz "SQL Server 2019 Standard on Windows Server 2019" do użycia na koncie laboratorium.|

### <a name="lab-settings"></a>Ustawienia laboratorium

Podczas konfigurowania laboratorium w klasie należy używać ustawień w poniższej tabeli.  Aby uzyskać więcej informacji na temat tworzenia laboratorium w klasie, zobacz [konfigurowanie samouczka laboratorium w klasie](tutorial-setup-classroom-lab.md).

| Ustawienia laboratorium | Wartość/instrukcja |
| ------------ | ------------------ |
|Rozmiar maszyny wirtualnej| Średnia. Ten rozmiar najlepiej nadaje się do relacyjnych baz danych, buforowania w pamięci i analizy.|
|Obraz maszyny wirtualnej| SQL Server 2019 Standard w systemie Windows Server 2019|

## <a name="template-machine-configuration"></a>Konfiguracja maszyny szablonu

Aby zainstalować mysql w systemie Windows Server 2019, można wykonać kroki wymienione w [install and Run MySQL Community Server na maszynie wirtualnej](https://docs.microsoft.com/previous-versions/azure/virtual-machines/windows/classic/mysql-2008r2?toc=%2Fazure%2Fvirtual-machines%2Fwindows%2Fclassic%2Ftoc.json#install-and-run-mysql-community-server-on-the-virtual-machine).

Program SQL Server 2019 jest wstępnie zainstalowany w obrazie maszyny wirtualnej, który wybraliśmy podczas tworzenia nowego laboratorium.

## <a name="cost-estimate"></a>Szacowanie kosztów

Pokryjmy możliwe szacunki kosztów dla tej klasy.  Użyjemy klasy 25 uczniów.  Zaplanowany czas zajęć jest 20 godzin.  Ponadto, każdy uczeń dostaje 10 godzin przydziału na pracę domową lub zadania poza zaplanowanym czasie zajęć.  Wybrany przez nas rozmiar maszyny wirtualnej był średni, czyli 42 jednostki laboratoryjne.

Oto przykład możliwego koszto preliminarzu dla tej klasy:

25 \* studentów (20 godzin planowych + \* 10 godzin kontyngentowych) 0.42 USD za godzinę = 315.00 USD

Więcej informacji na temat cen można znaleźć w [cenniku usług Azure Lab Services](https://azure.microsoft.com/pricing/details/lab-services/).

## <a name="conclusion"></a>Podsumowanie

W tym artykule oszukał kroki niezbędne do utworzenia laboratorium dla podstawowych koncepcji zarządzania bazami danych przy użyciu mysql i SQL Server. Można użyć podobnej konfiguracji dla innych klas baz danych.

## <a name="next-steps"></a>Następne kroki

Następne kroki są wspólne dla konfigurowania dowolnego laboratorium.

- [Tworzenie szablonu i zarządzanie nim](how-to-create-manage-template.md)
- [Dodaj użytkowników](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Ustawianie przydziału](how-to-configure-student-usage.md#set-quotas-for-users)
- [Ustawianie harmonogramu](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [Linki do rejestracji wiadomości e-mail do studentów](how-to-configure-student-usage.md#send-invitations-to-users)
