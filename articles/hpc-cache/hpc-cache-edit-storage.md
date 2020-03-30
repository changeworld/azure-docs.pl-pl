---
title: Aktualizowanie obiektów docelowych pamięci masowej HPC usługi Azure
description: Jak edytować obiekty docelowe magazynu hpc usługi Azure
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 1/08/2020
ms.author: rohogue
ms.openlocfilehash: 5635bfc6ea5faea41b125037c76c0b8635e0f528
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75867006"
---
# <a name="edit-storage-targets"></a>Edytowanie lokalizacji docelowych magazynu

Można usunąć lub zmodyfikować miejsce docelowe magazynu ze strony **obiektów docelowych magazynu** pamięci podręcznej.

## <a name="remove-a-storage-target"></a>Usuwanie miejsca docelowego magazynu

Aby usunąć miejsce docelowe magazynu, zaznacz go na liście i kliknij przycisk **Usuń.**

Ta akcja usuwa skojarzenia docelowego magazynu z tym systemem pamięci podręcznej HPC azure, ale nie zmienia systemu magazynu zaplecza. Na przykład jeśli użyto kontenera magazynu obiektów Blob platformy Azure, kontener i jego zawartość nadal istnieją po usunięciu go z pamięci podręcznej. Kontener można dodać do innej pamięci podręcznej HPC platformy Azure, ponownie dodać go do tej pamięci podręcznej lub usunąć za pomocą witryny Azure portal.

Wszelkie zmiany plików przechowywane w pamięci podręcznej są zapisywane w systemie magazynu zaplecza przed usunięciem miejsca docelowego magazynu. Ten proces może potrwać godzinę lub dłużej, jeśli wiele zmienionych danych znajduje się w pamięci podręcznej.

## <a name="update-storage-targets"></a>Aktualizowanie obiektów docelowych magazynu

Można edytować obiekty docelowe magazynu, aby zmodyfikować niektóre z ich właściwości. Różne właściwości są edytowalne dla różnych typów magazynu:

* W przypadku obiektów docelowych magazynu obiektów blob można zmienić ścieżkę obszaru nazw.

* W przypadku obiektów docelowych magazynu NFS można zmienić następujące właściwości:

  * Ścieżka obszaru nazw
  * Model użycia
  * Eksportowanie
  * Eksportowanie podkatalogu

Nie można edytować nazwy, typu ani systemu magazynu zaplecza magazynu magazynu (kontener obiektów Blob lub adres hosta/IP usługi NFS). Jeśli chcesz zmienić te właściwości, usuń miejsce docelowe magazynu i utwórz zastąpienie nową wartością.

Aby zmodyfikować miejsce docelowe magazynu, kliknij nazwę obiektu docelowego magazynu, aby otworzyć jego stronę szczegółów. Niektóre pola na stronie można edytować.

![zrzut ekranu strony edycji obiektu docelowego magazynu systemu plików NFS](media/hpc-cache-edit-storage-nfs.png)

## <a name="update-an-nfs-storage-target"></a>Aktualizowanie miejsca docelowego magazynu systemu plików NFS

W przypadku miejsca docelowego magazynu systemu plików NFS można zaktualizować kilka właściwości. (Zapoznaj się z powyższym zrzutem ekranu, aby uzyskać przykładową stronę edycji).

* **Model użycia** — model użycia wpływa na sposób przechowywania danych w pamięci podręcznej. Przeczytaj [artykuł Wybierz model użycia,](hpc-cache-add-storage.md#choose-a-usage-model) aby dowiedzieć się więcej.
* **Ścieżka wirtualnego obszaru nazw** — ścieżka używana przez klientów do instalowania tego miejsca docelowego magazynu. Przeczytaj [planowanie zagregowanego obszaru nazw, aby](hpc-cache-namespace.md) uzyskać szczegółowe informacje.
* **Ścieżka eksportu systemu plików NFS** — eksportowanie systemu magazynu do użycia dla tej ścieżki obszaru nazw.
* **Ścieżka podkatalogu** — podkatalog (w obszarze eksportu) do skojarzenia z tą ścieżką obszaru nazw. Pozostaw to pole puste, jeśli nie trzeba określać podkatalogu.

Każda ścieżka obszaru nazw wymaga unikatowej kombinacji eksportu i podkatalogu. Oznacza to, że nie można wykonać dwie różne ścieżki skierowane do klienta do dokładnie tego samego katalogu w systemie magazynu zaplecza.

Po wszuszczeniu kliknij przycisk **OK,** aby zaktualizować miejsce docelowe magazynu, lub kliknij przycisk **Anuluj,** aby odrzucić zmiany.

## <a name="update-an-azure-blob-storage-target"></a>Aktualizowanie obiektu docelowego magazynu obiektów Blob platformy Azure

Strona szczegółów obiektu docelowego magazynu obiektów blob umożliwia modyfikowanie ścieżki wirtualnego obszaru nazw.

![zrzut ekranu przedstawiający stronę edycji obiektu docelowego magazynu obiektów blob](media/hpc-cache-edit-storage-blob.png)

Po zakończeniu kliknij przycisk **OK,** aby zaktualizować miejsce docelowe magazynu, lub kliknij przycisk **Anuluj,** aby odrzucić zmiany.

## <a name="next-steps"></a>Następne kroki

* Przeczytaj [artykuł Dodaj obiekty docelowe magazynu,](hpc-cache-add-storage.md) aby dowiedzieć się więcej o tych opcjach.
* Przeczytaj [artykuł Planowanie zagregowanego obszaru nazw,](hpc-cache-namespace.md) aby uzyskać więcej wskazówek dotyczących korzystania ze ścieżek wirtualnych.
