---
title: Wprowadzenie do magazynu obiektów Blob — magazyn obiektów na platformie Azure | Microsoft Docs
description: Magazyn obiektów Blob platformy Azure służy do przechowywania dużych ilości danych obiektów niestrukturalnych, takich jak dane tekstowe lub binarne. Twoje aplikacje mają dostęp do obiektów w magazynie obiektów Blob za pomocą programu PowerShell lub interfejsu wiersza polecenia platformy Azure, z kodu za pomocą bibliotek klienta magazynu Azure lub za pośrednictwem REST.
services: storage
author: tamram
manager: jeconnoc
ms.service: storage
ms.topic: overview
ms.date: 03/27/2018
ms.author: tamram
ms.openlocfilehash: 0fff0032ec2452413bcd1df3175634b14a64208f
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2018
---
# <a name="introduction-to-blob-storage"></a>Wprowadzenie do usługi Blob Storage

Magazyn obiektów Blob platformy Azure to rozwiązanie magazynu w chmurze firmy Microsoft dla obiektów danych. Magazyn obiektów Blob może przechowywać olbrzymie ilości danych obiektów niestrukturalnych, takich jak dane tekstowe lub binarne. Dostęp do danych w magazynie obiektów Blob można uzyskać z dowolnego miejsca na świecie za pośrednictwem protokołu HTTP lub HTTPS. Magazyn obiektów Blob może być użyty do udostępniania danych publicznie lub do przechowywania danych aplikacji prywatnie.

Najczęstsze zastosowania usługi Blob Storage obejmują:

* Obsługiwanie obrazów i dokumentów bezpośrednio w przeglądarce
* Przechowywanie plików do dostępu rozproszonego
* Przesyłanie strumieniowe audio i wideo
* Zapisywanie w celu tworzenia kopii zapasowych, przywracania, odzyskiwania po awarii i archiwizowania
* Przechowywanie danych w celu analizy w usłudze lokalnej lub hostowanej na platformie Azure
* Przechowywanie wirtualnych dysków twardych do użycia z maszynami wirtualnymi platformy Azure

## <a name="blob-service-concepts"></a>Pojęcia dotyczące usługi Blob

Usługa Blob obejmuje następujące składniki:

![Architektura obiektów blob](./media/storage-blobs-introduction/blob1.png)

* **Konto magazynu:** cały dostęp do usługi Azure Storage odbywa się przez konto magazynu. To konto magazynu może być **kontem magazynu ogólnego przeznaczenia (w wersji 1 lub 2)** lub **kontem usługi Blob Storage**. Aby uzyskać więcej informacji, zobacz [About Azure storage accounts](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) (Informacje o kontach usługi Azure Storage).

* **Kontener:** kontener zawiera grupowanie zestawu obiektów blob. Wszystkie obiekty blob muszą być w kontenerze. Konto może zawierać nieograniczoną liczbę kontenerów. Kontener może przechowywać nieograniczoną liczbę obiektów blob. Pamiętaj, że wszystkie litery w nazwie kontenera muszą być małymi literami.

* **Obiekt blob:** plik dowolnego typu o dowolnym rozmiarze. Usługa Azure Storage udostępnia trzy typy obiektów blob: blokowe, [stronicowe](storage-blob-pageblob-overview.md) i uzupełnialne.
  
    *Blokowe obiekty blob* idealnie nadają się do przechowywania tekstu lub plików binarnych, takich dokumenty czy pliki multimedialne. *Uzupełnialne obiekty blob* są podobne do obiektów blokowych, ponieważ składają się z bloków, ale zoptymalizowane do operacji uzupełnialnych, więc przydatne w scenariuszach logowania. Pojedynczy blokowy obiekt blob może zawierać do 50 000 bloków do 100 MB każdy, których rozmiar całkowity może nieco przekraczać 4.75 TB (100 MB X 50 000). Pojedynczy uzupełniany obiekt blob może zawierać do 50 000 bloków do 4 MB każdy, których rozmiar całkowity może nieco przekraczać 195 GB (4 MB X 50 000).
  
    *Stronicowe obiekty blob* mogą mieć rozmiar maksymalnie 8 TB i są bardziej efektywne w przypadku operacji częstego odczytu/zapisu. Usługa Azure Virtual Machines używa stronicowych obiektów blob jako systemu operacyjnego lub dysków z danymi.
  
    Aby uzyskać szczegółowe informacje o nazewnictwie kontenerów i obiektów blob, zobacz temat [Naming and Referencing Containers, Blobs, and Metadata](/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata) (Nazewnictwo i odwoływanie się do kontenerów, obiektów blob i metadanych).

## <a name="next-steps"></a>Następne kroki

* [Tworzenie konta magazynu](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
* [Rozpoczynanie pracy z usługą Blob Storage przy użyciu platformy .NET](storage-dotnet-how-to-use-blobs.md)
* [Przykłady usługi Azure Storage korzystające z platformy .NET](../common/storage-samples-dotnet.md)
* [Przykłady usługi Azure Storage korzystające z języka Java](../common/storage-samples-java.md)
