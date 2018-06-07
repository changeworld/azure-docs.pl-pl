---
title: Wprowadzenie do magazynu Azure stosu
description: Więcej informacji na temat magazynu Azure stosu
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
ms.date: 05/21/2018
ms.author: mabrigg
ms.openlocfilehash: d97a5f8aff57f4bbfd7d5222a87d258fa5c92da8
ms.sourcegitcommit: 680964b75f7fff2f0517b7a0d43e01a9ee3da445
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34604390"
---
# <a name="introduction-to-azure-stack-storage"></a>Wprowadzenie do magazynu Azure stosu

*Dotyczy: Azure stosu zintegrowanych systemów i Azure stosu Development Kit*

## <a name="overview"></a>Przegląd

Magazyn Azure stosu to zestaw usługi magazynu w chmurze, który zawiera obiekty BLOB, tabel i kolejek, które są zgodne z usług magazynu Azure.

## <a name="azure-stack-storage-services"></a>Usług Azure stos magazynu

Usługa Azure storage stosu udostępnia następujące trzy usługi:

- **Blob Storage**

    Magazyn obiektów blob przechowuje dane obiektów bez struktury. Obiekt blob może być dowolnymi danymi tekstowymi lub binarnymi, takimi jak dokument, plik multimedialny lub instalator aplikacji.

- **Magazyn tabel**

    Table storage przechowuje zestawy danych ze strukturą. Magazyn tabel jest magazynem typu NoSQL zawierającym pary klucz-atrybut, co umożliwia szybkie opracowywanie i szybki dostęp do dużych ilości danych.

- **Magazyn kolejek**

    Magazyn kolejek umożliwia niezawodną obsługę komunikatów do przetwarzania przepływu pracy i komunikacji między składnikami usług w chmurze.

Konto magazynu platformy Azure stosu jest bezpieczne konto, które umożliwia dostęp do usług w usłudze Azure Storage stosu. Konto magazynu zapewnia unikatową przestrzeń nazw dla zasobów magazynu. Na poniższym diagramie przedstawiono relacje między zasobami magazynu Azure stosu na koncie magazynu:

![Omówienie usługi Azure stos magazynu](media/azure-stack-storage-overview/AzureStackStorageOverview.png)

### <a name="blob-storage"></a>Blob Storage

Dla użytkowników z dużej ilości danych bez struktury obiektów mają być przechowywane w chmurze magazynu obiektów blob oferuje efektywne i skalowalne rozwiązanie. Magazyn obiektów blob służy do przechowywania zawartości takiej jak:

- Dokumenty
- Dane społecznościowe takie jak zdjęcia, wideo, muzyka i blogi
- Kopie zapasowe plików, komputerów, baz danych i urządzeń
- Obrazy i tekst dla aplikacji sieci Web
- Dane konfiguracji dla aplikacji w chmurze
- Dane big data takie jak dzienniki i inne duże zestawy danych

Każdy obiekt blob znajduje się w kontenerze. Kontenery to także wygodny sposób na przypisywanie zasad zabezpieczeń do grup obiektów. Konto magazynu może zawierać dowolną liczbę kontenerów, a kontener może zawierać dowolną liczbę obiektów blob w granicach konta magazynu.

Magazyn obiektów blob udostępnia trzy typy obiektów blob:

- **Blokowe obiekty BLOB**

    Blokowe obiekty BLOB są zoptymalizowane pod kątem przesyłania strumieniowego i przechowywania obiektów w chmurze i dobrze nadają się do przechowywania dokumentów, plików multimedialnych, kopii zapasowych i itp.

- **Uzupełnialnych obiektów blob**

    Uzupełnialne obiekty blob są podobne do blokowych obiektów blob, lecz są zoptymalizowane pod kątem operacji dołączania. Uzupełnialny obiekt blob można zaktualizować tylko przez dodanie nowego bloku na końcu. Uzupełnialne obiekty blob są dobrym rozwiązaniem w przypadku scenariuszy takich jak rejestrowanie, które wymaga zapisywania tylko na końcu obiektu blob.

- **Stronicowe obiekty BLOB**

    Stronicowe obiekty BLOB są zoptymalizowane pod kątem reprezentowania dysków IaaS i obsługi losowych zapisuje, która jest maksymalnie 1 TB. IaaS plik VHD przechowywany jako stronicowy obiekt blob jest dysk dołączony do maszyny wirtualnej platformy Azure stosu.

### <a name="table-storage"></a>Magazyn tabel

Nowoczesne aplikacje często wymagają magazynu, który jest bardziej skalowalny i elastyczny, niż określają to wymagania poprzedniej generacji oprogramowania. Magazyn tabel jest skalowalny na ogromną skalę i oferuje wysoką dostępność, dzięki czemu aplikacja może automatycznie skalować się, aby spełnić wymagania użytkownika. Magazyn tabel jest magazynem kluczy/atrybutów NoSQL firmy Microsoft — ma on schematów, czym różni się od tradycyjnych relacyjnych baz danych. W wypadku magazynu danych bez schematów można łatwo zaadaptować dane do rozwijających się potrzeb aplikacji. Magazyn tabel jest łatwy w użyciu, dzięki czemu deweloperzy mogą szybko tworzyć aplikacje.

Magazyn tabel jest magazynem pary klucz atrybut, co oznacza, że każda wartość w tabeli są przechowywane z użyciem nazwy właściwości typu. Nazwa właściwości może służyć do filtrowania i określania kryteriów wyboru. Kolekcja właściwości i ich wartości stanowi jednostkę. Ponieważ Magazyn tabel nie korzysta ze schematów, dwie jednostki w tej samej tabeli mogą zawierać różne kolekcje właściwości i te właściwości mogą być różnych typów.

Magazyn tabel umożliwia przechowywanie elastycznych zestawów danych, takich jak dane użytkownika dla aplikacji sieci web, książki adresowe, informacje o urządzeniach i inne rodzaje metadanych, których wymaga Twoja usługa. Dla współczesnych aplikacji internetowych bazy danych NoSQL, takie jak magazyn tabel, stanowią popularną alternatywę do tradycyjnych relacyjnych baz danych.

Konto magazynu może zawierać dowolną liczbę tabel, a tabela może zawierać dowolną liczbę jednostek, aż do limitu pojemności konta magazynu.

### <a name="queue-storage"></a>Queue Storage

W przypadku projektowania aplikacji pod kątem skalowania składniki aplikacji są często rozłączane, dzięki czemu mogą być skalowane niezależnie. Magazyn kolejek zapewnia niezawodne rozwiązanie do obsługi komunikatów dla komunikacji asynchronicznej między składnikami aplikacji, czy jest na nich uruchomione w chmurze, na komputerze, na serwerze lokalnym lub na urządzeniu przenośnym. Magazyn kolejek obsługuje również zarządzanie asynchronicznymi zadaniami oraz przepływy pracy procesu kompilacji.

Konto magazynu może zawierać dowolną liczbę kolejek, a kolejka może zawierać dowolną liczbę komunikatów aż do limitu pojemności konta magazynu. Poszczególne wiadomości mogą mieć maksymalnie 64 KB.

## <a name="next-steps"></a>Kolejne kroki

- [Magazyn Azure spójne: różnice i zagadnienia](azure-stack-acs-differences.md)

- Aby dowiedzieć się więcej na temat usługi Azure Storage, zobacz [wprowadzenie do usługi Magazyn Microsoft Azure](../../storage/common/storage-introduction.md)
