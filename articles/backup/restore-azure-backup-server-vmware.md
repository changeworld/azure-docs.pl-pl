---
title: Przywracanie maszyn wirtualnych VMware przy użyciu usługi Azure Backup Server
description: Użyj usługi Azure Backup Server (MABS), aby przywrócić maszyny wirtualne VMware uruchomione na serwerze VMware vCenter/ESXi.
ms.topic: conceptual
ms.date: 08/18/2019
ms.openlocfilehash: ab2fb4f8f79fa5a664f5cb0ba1bb537c1df658c2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77212350"
---
# <a name="restore-vmware-virtual-machines"></a>Przywracanie maszyn wirtualnych VMware

W tym artykule wyjaśniono, jak przywrócić punkty odzyskiwania maszyn wirtualnych VMware za pomocą programu Microsoft Azure Backup Server (MABS). Aby uzyskać omówienie używania usługi MABS do odzyskiwania danych, zobacz [Odzyskiwanie chronionych danych](https://docs.microsoft.com/azure/backup/backup-azure-alternate-dpm-server). W konsoli administratora MABS istnieją dwa sposoby znajdowania danych, które można odzyskać — wyszukiwanie lub przeglądanie. Podczas odzyskiwania danych, może lub nie może chcesz przywrócić dane lub maszyny Wirtualnej do tej samej lokalizacji. Z tego powodu usługa MABS obsługuje trzy opcje odzyskiwania kopii zapasowych maszyn wirtualnych VMware:

* **Odzyskiwanie oryginalnej lokalizacji (OLR)** — użyj OLR, aby przywrócić chronione maszyny Wirtualnej do pierwotnej lokalizacji. Maszynę Wirtualną do pierwotnej lokalizacji można przywrócić do pierwotnej lokalizacji tylko wtedy, gdy nie dodano ani nie usunięto żadnych dysków, ponieważ wystąpiła kopia zapasowa. Jeśli dyski zostały dodane lub usunięte, należy użyć odzyskiwania lokalizacji alternatywnej.

* **Odzyskiwanie lokalizacji alternatywnej (ALR)** — gdy brakuje oryginalnej maszyny Wirtualnej lub nie chcesz przeszkadzać oryginalnej maszynie wirtualnej, odzyskaj maszynę wirtualną do lokalizacji alternatywnej. Aby odzyskać maszynę wirtualną do lokalizacji alternatywnej, należy podać lokalizację hosta ESXi, puli zasobów, folderu oraz magazynu magazynu i ścieżki. Aby ułatwić odróżnienie przywróconej maszyny Wirtualnej od oryginalnej maszyny Wirtualnej, program MABS dołącza "-Odzyskane" do nazwy maszyny Wirtualnej.

* **Odzyskiwanie lokalizacji poszczególnych plików (ILR)** — jeśli chroniona maszyna wirtualna jest maszyną wirtualną systemu Windows Server, poszczególne pliki/foldery wewnątrz maszyny Wirtualnej można odzyskać za pomocą funkcji ILR firmy MABS. Aby odzyskać poszczególne pliki, zobacz procedurę w dalszej części tego artykułu.

## <a name="restore-a-recovery-point"></a>Przywracanie punktu odzyskiwania

1. W konsoli administratora mabs kliknij pozycję Widok odzyskiwania.

2. Korzystając z okienka Przeglądaj, przeglądaj lub filtruj, aby znaleźć maszynę wirtualną, którą chcesz odzyskać. Po wybraniu maszyny Wirtualnej lub folderu w okienku Punkty odzyskiwania są wyświetlane dostępne punkty odzyskiwania.

    ![Dostępne punkty odzyskiwania](./media/restore-azure-backup-server-vmware/recovery-points.png)

3. W **polu Punkty odzyskiwania** użyj menu kalendarza i menu rozwijanego, aby wybrać datę, kiedy został wykonany punkt odzyskiwania. Daty kalendarza pogrubione mają dostępne punkty odzyskiwania.

4. Na wstążce narzędzia kliknij przycisk **Odzyskaj,** aby otworzyć **Kreatora odzyskiwania**.

    ![Kreator odzyskiwania, przejrzyj wybór odzyskiwania](./media/restore-azure-backup-server-vmware/recovery-wizard.png)

5. Kliknij **przycisk Dalej,** aby przejść do ekranu **Określ opcje odzyskiwania.**

6. Na ekranie **Określanie opcji odzyskiwania,** jeśli chcesz włączyć ograniczanie przepustowości sieci, kliknij przycisk **Modyfikuj**. Aby pozostawić ograniczenie dławienia sieci, kliknij przycisk **Dalej**. Żadne inne opcje na tym ekranie kreatora nie są dostępne dla maszyn wirtualnych VMware. Jeśli wybierzesz opcję modyfikowania przepustnicy przepustowości sieci, w oknie dialogowym Przepustnica wybierz pozycję **Włącz ograniczanie przepustowości sieci,** aby ją włączyć. Po włączeniu skonfiguruj **ustawienia** i **harmonogram pracy**.

7. Na ekranie **Wybierz typ odzyskiwania** wybierz, czy chcesz odzyskać do oryginalnego wystąpienia, czy do nowej lokalizacji, a następnie kliknij przycisk **Dalej**.

     * Jeśli wybierzesz **opcję Odzyskaj do oryginalnego wystąpienia,** nie musisz dokonywać żadnych innych wyborów w kreatorze. Dane dla oryginalnego wystąpienia jest używany.

     * Jeśli wybierzesz **opcję Odzyskaj jako maszynę wirtualną na dowolnym hoście,** na ekranie **Określ miejsce docelowe podaj** informacje dotyczące **hosta ESXi, puli zasobów, folderu** i **ścieżki**.

      ![Wybierz typ odzyskiwania](./media/restore-azure-backup-server-vmware/recovery-type.png)

8. Na ekranie **Podsumowanie** przejrzyj ustawienia i kliknij przycisk **Odzyskaj,** aby rozpocząć proces odzyskiwania. Ekran **Stanu odzyskiwania** pokazuje postęp operacji odzyskiwania.

## <a name="restore-an-individual-file-from-a-vm"></a>Przywracanie pojedynczego pliku z maszyny Wirtualnej

Można przywrócić poszczególne pliki z chronionego punktu odzyskiwania maszyny Wirtualnej. Ta funkcja jest dostępna tylko dla maszyn wirtualnych z systemem Windows Server. Przywracanie poszczególnych plików jest podobne do przywracania całej maszyny Wirtualnej, z wyjątkiem przeglądania w VMDK i znajdowania żądanych plików przed rozpoczęciem procesu odzyskiwania. Aby odzyskać pojedynczy plik lub wybrać pliki z maszyny Wirtualnej systemu Windows Server:

>[!NOTE]
>Przywracanie pojedynczego pliku z maszyny Wirtualnej jest dostępne tylko dla maszyn wirtualnych systemu Windows i punktów odzyskiwania dysku.

1. W konsoli administratora mabs kliknij pozycję Widok **odzyskiwania.**

2. Korzystając z **okienka Przeglądaj,** przeglądaj lub filtruj, aby znaleźć maszynę wirtualną, którą chcesz odzyskać. Po wybraniu maszyny Wirtualnej lub folderu w okienku Punkty odzyskiwania są wyświetlane dostępne punkty odzyskiwania.

    ![Dostępne punkty odzyskiwania](./media/restore-azure-backup-server-vmware/vmware-rp-disk.png)

3. W okienku **Punkty odzyskiwania dla:** użyj kalendarza, aby wybrać datę zawierającą żądane punkty odzyskiwania. W zależności od sposobu skonfigurowania zasad tworzenia kopii zapasowych daty mogą mieć więcej niż jeden punkt odzyskiwania. Po wybraniu dnia, w którym został podjęty punkt odzyskiwania, upewnij się, że wybrano prawidłowy **czas odzyskiwania.** Jeśli wybrana data ma wiele punktów odzyskiwania, wybierz punkt odzyskiwania, wybierając go w menu rozwijanym Czas odzyskiwania. Po wybraniu punktu odzyskiwania lista elementów do odzyskania pojawi się w okienku **Ścieżka:.**

4. Aby znaleźć pliki, które chcesz odzyskać, w okienku **Ścieżka** kliknij dwukrotnie element w kolumnie **Element do odzyskania,** aby go otworzyć. Wybierz plik, pliki lub foldery, które chcesz odzyskać. Aby zaznaczyć wiele elementów, naciśnij klawisz **Ctrl** podczas zaznaczania każdego elementu. Za pomocą **okienka Ścieżka** można przeszukiwać listę plików lub folderów znajdujących się w kolumnie **Element do odzyskania.** **Poniższa lista wyszukiwania** nie wyszukuje w podfolderach. Aby przeszukiwać podfoldery, kliknij dwukrotnie folder. Użyj przycisku **Up,** aby przenieść się z folderu podrzędnego do folderu nadrzędnego. Można wybrać wiele elementów (plików i folderów), ale muszą one znajdować się w tym samym folderze nadrzędnym. Nie można odzyskać elementów z wielu folderów w tym samym zadaniu odzyskiwania.

    ![Przejrzyj wybór odzyskiwania](./media/restore-azure-backup-server-vmware/vmware-rp-disk-ilr-2.png)

5. Po wybraniu elementów do odzyskania na wstążce narzędzia Konsola administratora kliknij przycisk **Odzyskaj,** aby otworzyć **Kreatora odzyskiwania**. W Kreatorze odzyskiwania na ekranie **Przejrzyj wybór odzyskiwania** pokazuje wybrane elementy do odzyskania.

6. Na ekranie **Określanie opcji odzyskiwania,** jeśli chcesz włączyć ograniczanie przepustowości sieci, kliknij przycisk **Modyfikuj**. Aby pozostawić ograniczenie dławienia sieci, kliknij przycisk **Dalej**. Żadne inne opcje na tym ekranie kreatora nie są dostępne dla maszyn wirtualnych VMware. Jeśli wybierzesz opcję modyfikowania przepustnicy przepustowości sieci, w oknie dialogowym Przepustnica wybierz pozycję **Włącz ograniczanie przepustowości sieci,** aby ją włączyć. Po włączeniu skonfiguruj **ustawienia** i **harmonogram pracy**.
7. Na ekranie **Wybierz typ odzyskiwania** kliknij przycisk **Dalej**. Pliki lub foldery można odzyskać tylko do folderu sieciowego.
8. Na ekranie **Określanie miejsca docelowego** kliknij przycisk **Przeglądaj,** aby znaleźć lokalizację sieciową dla plików lub folderów. Mabs tworzy folder, w którym kopiowane są wszystkie odzyskane elementy. Nazwa folderu ma prefiks MABS_day-miesiąc roku. Po wybraniu lokalizacji odzyskanych plików lub folderu zostaną podane szczegółowe informacje dotyczące tej lokalizacji (miejsce docelowe, ścieżka docelowa i dostępne miejsce).

    ![Określanie lokalizacji do odzyskania plików](./media/restore-azure-backup-server-vmware/specify-destination.png)

9. Na ekranie **Określanie opcji odzyskiwania** wybierz ustawienie zabezpieczeń, które ma być stosowane. Można zmodyfikować ograniczanie użycia przepustowości sieci, ale ograniczanie przepustowości jest domyślnie wyłączone. Ponadto **odzyskiwanie sieci SAN** i **powiadomienia** nie są włączone.
10. Na ekranie **Podsumowanie** przejrzyj ustawienia i kliknij przycisk **Odzyskaj,** aby rozpocząć proces odzyskiwania. Ekran **Stanu odzyskiwania** pokazuje postęp operacji odzyskiwania.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać informacje na temat rozwiązywania problemów podczas korzystania z usługi Azure Backup Server, zapoznaj się z [przewodnikiem rozwiązywania problemów dla usługi Azure Backup Server](./backup-azure-mabs-troubleshoot.md).
