---
title: Kontener profilu Magazynu FSLogix Windows Virtual Desktop — Azure
description: Opcje przechowywania profilu FSLogix pulpitu wirtualnego systemu Windows w usłudze Azure Storage.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 10/14/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 71ba24784dee7771acbe19bf0261c7dc02478b24
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127522"
---
# <a name="storage-options-for-fslogix-profile-containers-in-windows-virtual-desktop"></a>Opcje przechowywania kontenerów profilu FSLogix w wirtualnym pulpicie systemu Windows

Platforma Azure oferuje wiele rozwiązań magazynu, których można używać do przechowywania kontenera profilu FSLogix. W tym artykule porównano rozwiązania magazynu, które platforma Azure oferuje dla kontenerów profilu użytkownika Windows Virtual Desktop FSLogix.

Pulpit wirtualny systemu Windows oferuje kontenery profilu FSLogix jako zalecane rozwiązanie profilu użytkownika. FSLogix jest przeznaczony do poruszania się po profilach w środowiskach zdalnych komputerów, takich jak pulpit wirtualny systemu Windows. Podczas logowania ten kontener jest dynamicznie dołączany do środowiska komputerowego przy użyciu natywnie obsługiwanego wirtualnego dysku twardego (VHD) i wirtualnego dysku twardego funkcji Hyper-V (VHDX). Profil użytkownika jest natychmiast dostępny i pojawia się w systemie dokładnie tak samo jak natywny profil użytkownika.

W poniższych tabelach porównano rozwiązania magazynu, które usługa Azure Storage oferuje dla profilów kontenerów kontenerów kontenerów pulpitu wirtualnego systemu Windows dla systemu Windows.

## <a name="azure-platform-details"></a>Szczegóły platformy Azure

|Funkcje|Azure Files|Azure NetApp Files|Bezpośrednie miejsca do magazynowania|
|--------|-----------|------------------|---------------------|
|Przypadek użycia|Zastosowania ogólne|Ultrawydajność lub migracja z netapp lokalnie|Wieloplatformowość|
|Usługa platformy|Tak, rozwiązanie natywne dla platformy Azure|Tak, rozwiązanie natywne dla platformy Azure|Nie, zarządzane samodzielnie|
|Dostępność regionalna|Wszystkie regiony|[Wybierz regiony](https://azure.microsoft.com/global-infrastructure/services/?products=netapp&regions=all)|Wszystkie regiony|
|Nadmiarowość|Lokalnie nadmiarowe/strefowo nadmiarowe/geograficznie nadmiarowe|Lokalnie nadmiarowe|Lokalnie nadmiarowe/strefowo nadmiarowe/geograficznie nadmiarowe|
|Poziomy i wydajność|Standardowa<br>Premium<br>Do max 100k IOPS na akcję z 5 GBps na akcję przy około 3 ms opóźnienia|Standardowa<br>Premium<br>Ultra<br>Do 320 k (16K) IOPS z 4,5 GBps na objętość przy opóźnieniu około 1 ms|Standardowy dysk twardy: do 500 limitów IOPS na dysk<br>Standardowy dysk SSD: do 4k IOPS na dysk<br>Dysk SSD premium: do 20 000 IOPS na dysk<br>Zalecamy dyski Premium do bezpośredniego przechowywania miejsc|
|Pojemność|100 TiB na jedną akcję|100 TiB na wolumin, do 12,5 PiB na subskrypcję|Maksymalnie 32 TiB na dysk|
|Wymagana infrastruktura|Minimalny rozmiar udziału 1 GiB|Minimalna pula pojemności 4 TiB, min rozmiar głośności 100 GiB|Dwie maszyny wirtualne w usłudze Azure IaaS (+ Cloud Witness) lub co najmniej trzy maszyny wirtualne bez dysków i koszty dla dysków|
|Protokoły|SMB 2.1/3. i REST|NFSv3, NFSv4.1 (wersja zapoznawcza), SMB 3.x/2.x|NFSv3, NFSv4.1, SMB 3.1|

## <a name="azure-management-details"></a>Szczegóły zarządzania platformy Azure

|Funkcje|Azure Files|Azure NetApp Files|Bezpośrednie miejsca do magazynowania|
|--------|-----------|------------------|---------------------|
|Dostęp|Chmura, lokalnie i hybrydowe (synchronizacja plików platformy Azure)|Chmura lokalna (za pośrednictwem usługi ExpressRoute)|Chmura lokalna|
|Tworzenie kopii zapasowych|Integracja migawki kopii zapasowej platformy Azure|Migawki plików NetApp platformy Azure|Integracja migawki kopii zapasowej platformy Azure|
|Bezpieczeństwo i zgodność|[Wszystkie certyfikaty obsługiwane przez platformę Azure](https://www.microsoft.com/trustcenter/compliance/complianceofferings)|Iso zakończone|[Wszystkie certyfikaty obsługiwane przez platformę Azure](https://www.microsoft.com/trustcenter/compliance/complianceofferings)|
|Integracja z usługą Azure Active Directory|[Natywna usługa Active Directory i Usługi domenowe Active Directory platformy Azure](https://docs.microsoft.com/azure/storage/files/storage-files-active-directory-overview)|[Usługi domenowe usługi Active Directory platformy Azure i natywna usługa Active Directory](../azure-netapp-files/azure-netapp-files-faqs.md#does-azure-netapp-files-support-azure-active-directory)|Tylko natywna usługa Active Directory lub usługi domenowe Usługi domenowe Active Directory platformy Azure|

Po wybraniu metody przechowywania zapoznaj się z [cennikami pulpitu wirtualnego systemu Windows, aby](https://azure.microsoft.com/pricing/details/virtual-desktop/) uzyskać informacje o naszych planach cenowych.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o kontenerach profilu FSLogix, dyskach profilu użytkownika i innych technologiach profilu użytkownika, zobacz tabelę w [kontenerach profilu FSLogix i plikach platformy Azure.](fslogix-containers-azure-files.md)

Jeśli jesteś gotowy do tworzenia własnych kontenerów profilu FSLogix, rozpocznij pracę z jednym z następujących samouczków:

- [Wprowadzenie do kontenerów profilu FSLogix w plikach azure w wirtualnym pulpicie systemu Windows](https://techcommunity.microsoft.com/t5/Windows-IT-Pro-Blog/Getting-started-with-FSLogix-profile-containers-on-Azure-Files/ba-p/746477)
- [Tworzenie kontenera profilu FSLogix dla puli hostów przy użyciu plików NetApp platformy Azure](create-fslogix-profile-container.md)
- Instrukcje w [deploy a two-node Storage Spaces Direct skalowany w poziomie serwera plików dla magazynu UPD na platformie Azure](/windows-server/remote/remote-desktop-services/rds-storage-spaces-direct-deployment/) również stosuje się podczas korzystania z kontenera profilu FSLogix zamiast dysku profilu użytkownika

Można również rozpocząć od samego początku i skonfigurować własne rozwiązanie Pulpit wirtualny systemu Windows w [Tworzenie dzierżawy w Windows Virtual Desktop](tenant-setup-azure-active-directory.md).
