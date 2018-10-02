---
title: Wprowadzenie do magazynu usługi Azure Stack
description: Więcej informacji na temat magazynu usługi Azure Stack
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.assetid: 092aba28-04bc-44c0-90e1-e79d82f4ff42
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/28/2018
ms.author: mabrigg
ms.openlocfilehash: 13fdf3257ed44212f45eeb3d2820a2022f54d777
ms.sourcegitcommit: 5843352f71f756458ba84c31f4b66b6a082e53df
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/01/2018
ms.locfileid: "47585242"
---
# <a name="introduction-to-azure-stack-storage"></a>Wprowadzenie do magazynu usługi Azure Stack

*Dotyczy: Usługa Azure Stack zintegrowane systemy i usługi Azure Stack Development Kit*

## <a name="overview"></a>Przegląd

Magazyn usługi Azure Stack to zestaw usług magazynu w chmurze, który zawiera obiekty BLOB, tabele i kolejki, które są zgodne z usługami Azure Storage.

## <a name="azure-stack-storage-services"></a>Usługi Azure Storage stosu

Magazyn usługi Azure Stack oferuje poniższe trzy usługi:

- **Blob Storage**

    Magazyn obiektów blob przechowuje dane obiektów bez struktury. Obiekt blob może być dowolnymi danymi tekstowymi lub binarnymi, takimi jak dokument, plik multimedialny lub instalator aplikacji.

- **Table Storage**

    Usługa TABLE storage przechowuje zestawy danych ze strukturą. Magazyn tabel jest magazynem typu NoSQL zawierającym pary klucz-atrybut, co umożliwia szybkie opracowywanie i szybki dostęp do dużych ilości danych.

- **Usługa Queue Storage**

    Usługa queue storage umożliwia niezawodną obsługę komunikatów dla przetwarzania przepływu pracy i komunikacji między składnikami usług w chmurze.

Konta magazynu usługi Azure Stack to bezpieczne konto, które zapewnia dostęp do usług w usłudze Azure Storage stosu. Konto magazynu zapewnia unikatową przestrzeń nazw dla zasobów magazynu. Na poniższym diagramie przedstawiono relacje między zasobami magazynu usługi Azure Stack w ramach konta magazynu:

![Omówienie usługi Azure Storage stosu](media/azure-stack-storage-overview/AzureStackStorageOverview.png)

### <a name="blob-storage"></a>Blob Storage

Dla użytkowników z dużej ilości danych obiektów bez struktury, do przechowywania w chmurze blob storage oferuje efektywne i skalowalne rozwiązanie. Usługi blob storage służy do przechowywania zawartości takiej jak:

- Dokumenty
- Dane społecznościowe takie jak zdjęcia, wideo, muzyka i blogi
- Kopie zapasowe plików, komputerów, baz danych i urządzeń
- Obrazy i tekst dla aplikacji internetowych
- Dane konfiguracji dla aplikacji w chmurze
- Dane big data takie jak dzienniki i inne duże zestawy danych

Każdy obiekt blob znajduje się w kontenerze. Kontenery to także wygodny sposób na przypisywanie zasad zabezpieczeń do grup obiektów. Konto magazynu może zawierać dowolną liczbę kontenerów, a kontener może zawierać dowolną liczbę obiektów blob do wysokości limitu konta magazynu.

Usługa blob storage oferuje trzy typy obiektów blob:

- **Obiekty BLOB typu Block**

    Blokowe obiekty BLOB są zoptymalizowane pod kątem przesyłania strumieniowego i przechowywania obiektów w chmurze i dobrze nadają się do przechowywania dokumentów, plików multimedialnych, kopii zapasowych i itp.

- **Uzupełnialne obiekty BLOB**

    Uzupełnialne obiekty blob są podobne do blokowych obiektów blob, lecz są zoptymalizowane pod kątem operacji dołączania. Uzupełnialny obiekt blob można zaktualizować tylko przez dodanie nowego bloku na końcu. Uzupełnialne obiekty blob są dobrym rozwiązaniem w przypadku scenariuszy takich jak rejestrowanie, które wymaga zapisywania tylko na końcu obiektu blob.

- **Stronicowe obiekty BLOB**

    Stronicowe obiekty BLOB są zoptymalizowane pod kątem reprezentowania dysków IaaS i obsługi losowych zapisu, która jest w rozmiarze do 1 TB. Maszynę wirtualną usługi Azure Stack dysk IaaS dołączony plik VHD przechowywany jako stronicowy obiekt blob.

### <a name="table-storage"></a>Magazyn tabel

Nowoczesne aplikacje często wymagają magazynu, który jest bardziej skalowalny i elastyczny, niż określają to wymagania poprzedniej generacji oprogramowania. Magazyn tabel jest skalowalny na ogromną skalę i oferuje wysoką dostępność, dzięki czemu aplikacja może automatycznie skalować się, aby spełnić wymagania użytkownika. Magazyn tabel to magazyn klucz atrybut NoSQL firmy Microsoft — korzysta ze schematów, czym różni się od tradycyjnych relacyjnych baz danych. W wypadku magazynu danych bez schematów można łatwo zaadaptować dane do rozwijających się potrzeb aplikacji. Magazyn tabel jest łatwy w użyciu, dzięki czemu deweloperzy mogą szybko tworzyć aplikacje.

Magazyn tabel to magazyn klucz atrybut, co oznacza, że każda wartość w tabeli są przechowywane z nazwą właściwości z określonym typem. Nazwa właściwości może służyć do filtrowania i określania kryteriów wyboru. Kolekcja właściwości i ich wartości stanowi jednostkę. Ponieważ usługi table storage korzysta ze schematów, dwie jednostki w tej samej tabeli mogą zawierać różne kolekcje właściwości, a te właściwości mogą być różnych typów.

Magazyn tabel umożliwia przechowywanie elastycznych zestawów danych, takich jak dane użytkownika dla aplikacji sieci web, książek adresowych, informacji o urządzeniach i inne rodzaje metadanych, których wymaga Twoja usługa. Dla współczesnych aplikacji internetowych baz danych NoSQL, takie jak magazyn tabel, stanowią popularną alternatywę do tradycyjnych relacyjnych baz danych.

Konto magazynu może zawierać dowolną liczbę tabel, a tabela może zawierać dowolną liczbę jednostek do limitu pojemności konta magazynu.

### <a name="queue-storage"></a>Queue Storage

W przypadku projektowania aplikacji pod kątem skalowania składniki aplikacji są często rozłączane, dzięki czemu mogą być skalowane niezależnie. Usługa queue storage zapewnia niezawodne rozwiązanie do obsługi komunikatów dla komunikacji asynchronicznej między składnikami aplikacji, czy są one uruchamiane w chmurze, na komputerze, na serwerze lokalnym lub na urządzeniu przenośnym. Magazyn kolejek obsługuje również zarządzanie asynchronicznymi zadaniami oraz przepływy pracy procesu kompilacji.

Konto magazynu może zawierać dowolną liczbę kolejek, a kolejka może zawierać dowolną liczbę komunikatów aż do limitu pojemności konta magazynu. Poszczególne wiadomości mogą mieć maksymalnie 64 KB.

## <a name="next-steps"></a>Kolejne kroki

- [Spójnych z platformą Azure storage: różnice i zagadnienia](azure-stack-acs-differences.md)

- Aby dowiedzieć się więcej o usłudze Azure Storage, zobacz [wprowadzenie do usługi Microsoft Azure Storage](../../storage/common/storage-introduction.md)
