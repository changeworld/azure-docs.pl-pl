---
title: Co to jest Azure FarmBeats
description: Zawiera omówienie usługi Azure FarmBeats
author: uhabiba04
ms.topic: overview
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 6f0a782309edc33a8a5ce661652922494ead2ec0
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2020
ms.locfileid: "80667306"
---
# <a name="overview-of-azure-farmbeats-preview"></a>Omówienie platformy Azure FarmBeats (wersja zapoznawcza)

Azure FarmBeats to oferta biznesowa dostępna w portalu Azure Marketplace. Umożliwia agregację zbiorów danych dotyczących rolnictwa między dostawcami. Usługa Azure FarmBeats umożliwia tworzenie modeli sztucznej inteligencji (AI) lub uczenia maszynowego (ML) na podstawie połączonych zestawów danych. Za pomocą platformy Azure FarmBeats firmy rolnicze mogą skupić się na podstawowych wartości dodaje zamiast niezróżniłego podnoszenia ciężkich inżynierii danych.

> [!NOTE]
> Azure FarmBeats jest obecnie w publicznej wersji zapoznawczej. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Azure FarmBeats jest dostarczana bez umowy o poziomie usług. Skorzystaj z [forum Azure FarmBeats,](https://aka.ms/FarmBeatsMSDN ) aby uzyskać pomoc techniczną.

![Projekt Farm Beats](./media/architecture-for-farmbeats/farmbeats-architecture-1.png)

Za pomocą wersji zapoznawczej usługi Azure FarmBeats możesz:

- Oceniaj zdrowie gospodarstw rolnych na podstawie indeksu roślinności i indeksu wody na podstawie zdjęć satelitarnych.
- Uzyskaj zalecenia dotyczące liczby czujników wilgotności gleby do użycia i gdzie je umieścić.
- Śledź warunki w gospodarstwie, wizualizując dane naziemne zebrane przez czujniki różnych dostawców.
- Pobierz mapę wilgotności gleby na podstawie fuzji danych satelitarnych i czujników.
- Uzyskaj wgląd w informacje o działaniu, tworząc modele AI/ML na podstawie zagregowanych zestawów danych.
- Zbuduj lub rozszerowuj swoje rozwiązanie w dziedzinie rolnictwa cyfrowego, zapewniając doradztwo w zakresie zdrowia w gospodarstwach rolnych.

## <a name="datahub"></a>Datahub (polski)

Usługa Azure FarmBeats Datahub jest warstwą interfejsu API, która umożliwia agregację, normalizację i kontekstowanie różnych zestawów danych rolnictwa między dostawcami. Możesz użyć usługi Azure FarmBeats, aby uzyskać:
- **Dane z czujników** od dwóch dostawców [czujników Davis Instruments](https://www.davisinstruments.com/product/enviromonitor-gateway/), [Teralytic](https://teralytic.com/), [Pessl Instruments](https://metos.at/)
- **Zdjęcia satelitarne** z misji satelitarnej [Sentinel-2](https://sentinel.esa.int/web/sentinel/home) Europejskiej Agencji Kosmicznej
- **Zdjęcia dronów** z trzech dostawców zdjęć dronów [senseFly](https://www.sensefly.com/) , [SlantRange](https://slantrange.com/) , [DJI](https://dji.com/)

Datahub został zaprojektowany jako rozszerzalna platforma interfejsu API. Współpracujemy z wieloma dostawcami, aby zintegrować się z platformą Azure FarmBeats, dzięki czemu masz większy wybór podczas tworzenia rozwiązania.

## <a name="accelerator"></a>Akcelerator

Akcelerator Azure FarmBeats to przykładowa aplikacja sieci web, która jest zbudowana na podstawie usługi Datahub. Akcelerator przyspiesza interfejs użytkownika i tworzenie modelu. Akcelerator Azure FarmBeats używa interfejsów API platformy Azure FarmBeats. Wizualizuje połkniętych danych z czujników jako wykresy i dane wyjściowe modelu jako mapy. Na przykład można użyć akceleratora, aby szybko utworzyć farmę i uzyskać mapę indeksu roślinności lub mapę umieszczenia czujnika dla tej farmy.

## <a name="role-based-access-control-rbac"></a>Kontrola dostępu oparta na rolach (RBAC)

Administrator może zdefiniować reguły dostępu dla usługi Azure FarmBeats przy użyciu jednej ze wstępnie zdefiniowanych ról. Role określają, do jakich obszarów aplikacji użytkownik ma dostęp i jakie akcje mogą wykonywać. Istnieją dwa rodzaje ról w usłudze Azure FarmBeats — dla użytkowników i partnerów.

### <a name="user-roles"></a>Role użytkowników

[Administrator może dodawać użytkowników i zarządzać nimi](manage-users-in-azure-farmbeats.md) oraz definiować ich poziomy dostępu na podstawie dwóch ról użytkowników: Administratora i Tylko do odczytu.

### <a name="partner-roles"></a>Role partnerów

Administrator może dodać wielu partnerów jako dostawców danych do usługi Azure FarmBeats. Poniżej podsumować dostępne role partnera w FarmBeats i ich uprawnienia:

| Typ partnera    |   Akcje  | Zakres |
| ---- | -------- | -------- |
| Partner czujnika  |   Tworzenie, odczytywanie, aktualizowanie <br/> <br/> Odczyt, aktualizowanie | DeviceModel, Urządzenie, CzujnikModel, Czujnik <br/> <br/> Rozszerzony typ |
| Partner w zdjęciach  |   Tworzenie, odczytywanie, aktualizowanie <br/> <br/> Odczyt, aktualizowanie <br/> <br/> Odczyt | Scena, SceneFile <br/> <br/> Rozszerzony typ <br/> <br/> Gospodarstwa |
| Partner pogodowy* <br/> <br/>  (* Już wkrótce) |   Tworzenie, odczytywanie, aktualizowanie <br/> <br/> Odczyt, aktualizowanie <br/> <br/> Odczyt | WeatherDataModel, PogodaDataLocation, JobType <br/> <br/> Rozszerzony typ <br/> <br/> Gospodarstwa |

## <a name="resources"></a>Resources

Usługa Azure FarmBeats jest oferowana bez dodatkowych opłat i płacisz tylko za zasoby platformy Azure, których używasz. Możesz użyć poniższych zasobów, aby dowiedzieć się więcej o ofercie:

- Bądź na bieżąco z najnowszymi wiadomościami dotyczącymi platformy Azure FarmBeats, odwiedzając nasz [blog Azure FarmBeats.](https://aka.ms/farmbeatsblog)
- Poszukaj pomocy, publikując pytanie na naszym [forum pomocy technicznej Usługi Azure FarmBeats](https://aka.ms/farmbeatssupport).
- Przekaż opinię, publikując lub głosując za pomysłem na funkcję na naszym [forum opinii Usługi Azure FarmBeats.](https://aka.ms/farmbeatsfeedback)

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Zainstaluj projekt Azure FarmBeats](install-azure-farmbeats.md)
