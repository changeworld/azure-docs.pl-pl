---
title: Strefy czasowe wystąpienia zarządzanego
description: Dowiedz się więcej o specyfice strefy czasowej wystąpienia zarządzanego usługi Azure SQL Database
services: sql-database
ms.service: sql-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MladjoA
ms.author: mlandzic
ms.reviewer: ''
ms.date: 09/03/2019
ms.openlocfilehash: 0dbed3db8e106b9bfe1b48ff2b9bc52840fc4c3a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79256096"
---
# <a name="time-zones-in-azure-sql-database-managed-instance"></a>Strefy czasowe w wystąpieniu zarządzanym usługi Azure SQL Database

Skoordynowany czas uniwersalny (UTC) jest zalecaną strefą czasową dla warstwy danych rozwiązań w chmurze. Wystąpienie zarządzanej bazy danych SQL azure oferuje również wybór stref czasowych, aby zaspokoić potrzeby istniejących aplikacji, które przechowują wartości daty i godziny oraz funkcje daty i godziny wywołania z niejawnym kontekstem określonej strefy czasowej.

Funkcje T-SQL, takie jak [GETDATE()](https://docs.microsoft.com/sql/t-sql/functions/getdate-transact-sql) lub kod CLR, obserwują strefę czasową ustawioną na poziomie wystąpienia. Zadania programu SQL Server Agent również postępuj zgodnie z harmonogramami zgodnie ze strefą czasową wystąpienia.

  >[!NOTE]
  > Wystąpienie zarządzane jest jedyną opcją wdrażania usługi Azure SQL Database, która obsługuje ustawienie strefy czasowej. Inne opcje wdrażania zawsze należy wykonać UTC.
Użyj [w strefie czasowej](https://docs.microsoft.com/sql/t-sql/queries/at-time-zone-transact-sql) w pojedynczych i pulowanych bazach danych SQL, jeśli chcesz interpretować informacje o dacie i godzinie w strefie czasowej innej niż UTC.

## <a name="supported-time-zones"></a>Obsługiwane strefy czasowe

Zestaw obsługiwanych stref czasowych jest dziedziczony z podstawowego systemu operacyjnego wystąpienia zarządzanego. Jest regularnie aktualizowana, aby uzyskać nowe definicje stref czasowych i odzwierciedlić zmiany w istniejących.

[Zmiana czasu letniego/zmiany strefy czasowej](https://aka.ms/time) gwarantuje historyczną dokładność od 2010 do przodu.

Lista z nazwami obsługiwanych stref czasowych jest widoczna za pośrednictwem widoku systemu [sys.time_zone_info.](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-time-zone-info-transact-sql)

## <a name="set-a-time-zone"></a>Ustawianie strefy czasowej

Strefę czasową wystąpienia zarządzanego można ustawić tylko podczas tworzenia wystąpienia. Domyślną strefą czasową jest UTC.

  >[!NOTE]
  > Nie można zmienić strefy czasowej istniejącego wystąpienia zarządzanego.

### <a name="set-the-time-zone-through-the-azure-portal"></a>Ustawianie strefy czasowej za pośrednictwem witryny Azure portal

Po wprowadzeniu parametrów dla nowego wystąpienia wybierz strefę czasową z listy obsługiwanych stref czasowych.
  
![Ustawianie strefy czasowej podczas tworzenia wystąpienia](media/sql-database-managed-instance-timezone/01-setting_timezone-during-instance-creation.png)

### <a name="azure-resource-manager-template"></a>Szablon usługi Azure Resource Manager

Określ właściwość timezoneId w [szablonie Menedżera zasobów,](https://aka.ms/sql-mi-create-arm-posh) aby ustawić strefę czasową podczas tworzenia wystąpienia.

```json
"properties": {
                "administratorLogin": "[parameters('user')]",
                "administratorLoginPassword": "[parameters('pwd')]",
                "subnetId": "[parameters('subnetId')]",
                "storageSizeInGB": 256,
                "vCores": 8,
                "licenseType": "LicenseIncluded",
                "hardwareFamily": "Gen5",
                "collation": "Serbian_Cyrillic_100_CS_AS",
                "timezoneId": "Central European Standard Time"
            },

```

Lista obsługiwanych wartości dla właściwości timezoneId znajduje się na końcu tego artykułu.

Jeśli nie zostanie określony, strefa czasowa jest ustawiona na UTC.

## <a name="check-the-time-zone-of-an-instance"></a>Sprawdzanie strefy czasowej wystąpienia

Funkcja [CURRENT_TIMEZONE](https://docs.microsoft.com/sql/t-sql/functions/current-timezone-transact-sql) zwraca nazwę wyświetlaną strefy czasowej wystąpienia.

## <a name="cross-feature-considerations"></a>Zagadnienia dotyczące różnych funkcji

### <a name="restore-and-import"></a>Przywracanie i importowanie

Można przywrócić plik kopii zapasowej lub zaimportować dane do wystąpienia zarządzanego z wystąpienia lub serwera z różnymi ustawieniami strefy czasowej. Należy to zrobić z ostrożnością. Analizowanie zachowania aplikacji i wyników kwerend i raportów, podobnie jak podczas przesyłania danych między dwoma wystąpieniami programu SQL Server z różnymi ustawieniami strefy czasowej.

### <a name="point-in-time-restore"></a>Przywracanie do określonego momentu

Podczas przywracania punktu w czasie czas przywracania jest interpretowany jako czas UTC. W ten sposób unika się wszelkich niejasności spowodowanych zmianami czasu letniego i jego potencjalnymi zmianami.

### <a name="auto-failover-groups"></a>Grupy automatycznego trybu failover

Korzystanie z tej samej strefy czasowej w wystąpieniu podstawowym i pomocniczym w grupie trybu failover nie jest wymuszane, ale zdecydowanie ją zalecamy.

  >[!WARNING]
  > Zdecydowanie zaleca się używanie tej samej strefy czasowej dla wystąpienia podstawowego i pomocniczego w grupie trybu failover. Ze względu na niektóre przypadki użycia rzadko zachowując tę samą strefę czasową w wystąpieniach podstawowych i pomocniczych nie jest wymuszane. Ważne jest, aby zrozumieć, że w przypadku ręcznego lub automatycznego trybu failover wystąpienie pomocnicze zachowa swoją oryginalną strefę czasową.

## <a name="limitations"></a>Ograniczenia

- Nie można zmienić strefy czasowej istniejącego wystąpienia zarządzanego.
- Procesy zewnętrzne uruchomione z zadań programu SQL Server Agent nie są zgodne ze strefą czasową wystąpienia.

## <a name="list-of-supported-time-zones"></a>Lista obsługiwanych stref czasowych

| **Identyfikator strefy czasowej** | **Nazwa wyświetlana strefy czasowej** |
| --- | --- |
| Dateline Godzina standardowa | (UTC-12:00) Międzynarodowa data na zachód |
| UTC-11 | (UTC-11:00) Skoordynowany uniwersalny czas-11 |
| Aleutian Czas standardowy | (UTC-10:00) Wyspy Aleuckie |
| Hawajski czas standardowy | (UTC-10:00) Hawaje |
| Marquesas (czas standardowy) | (UTC-09:30) Wyspy Marquesas |
| Alaskan (czas standardowy) | (UTC-09:00) Alaska |
| UTC-09 | (UTC-09:00) Skoordynowany czas uniwersalny-09 |
| Pacyficzny czas standardowy (Meksyk) | (UTC-08:00) Baja Kalifornia |
| UTC-08 | (UTC-08:00) Skoordynowany czas uniwersalny-08 |
| Pacyficzny czas standardowy | (UTC-08:00) Czas pacyficzny (USA & Kanada) |
| Amerykański górski czas standardowy | (UTC-07:00) Arizona |
| Górski czas standardowy (Meksyk) | (UTC-07:00) Chihuahua, La Paz, Mazatlan |
| Górski czas standardowy | (UTC-07:00) Czas górski (USA & Kanada) |
| Ameryka Środkowa (czas standardowy) | (UTC-06:00) Ameryka Środkowa |
| Centralny czas standardowy | (UTC-06:00) Czas centralny (USA & Kanada) |
| Wyspa Wielkanocna Czas standardowy | (UTC-06:00) Wyspa Wielkanocna |
| Centralny czas standardowy (Meksyk) | (UTC-06:00) Guadalajara, Meksyk, Monterrey |
| Kanada Centralny czas standardowy | (UTC-06:00) Saskatchewan |
| SA Pacyfik (czas standardowy) | (UTC-05:00) Bogota, Lima, Quito, Rio Branco |
| Wschodni czas standardowy (Meksyk) | (UTC-05:00) Chetumal |
| Wschodni czas standardowy | (UTC-05:00) Czas wschodni (Usa & Kanada) |
| Czas standardowy na Haiti | (UTC-05:00) Haiti |
| Kuba Czas standardowy | (UTC-05:00) Hawana |
| Wschodni czas amerykański | (UTC-05:00) Indiana (Wschód) |
| Turks i Caicos czas standardowy | (UTC-05:00) Turks i Caicos |
| Paragwaj (czas standardowy) | (UTC-04:00) Asuncion |
| Atlantycki czas standardowy | (UTC-04:00) Czas atlantycki (Kanada) |
| Wenezuela (czas standardowy) | (UTC-04:00) Caracas |
| Centralny brazylijski czas standardowy | (UTC-04:00) Cuiaba |
| SA Zachodni czas standardowy | (UTC-04:00) Georgetown, La Paz, Manaus, San Juan |
| Pacific SA (czas standardowy) | (UTC-04:00) Santiago |
| Nowa Fundlandia (czas standardowy) | (UTC-03:30) Newfoundland |
| Tocantins Czas standardowy | (UTC-03:00) Araguaina ( Araguaina ) |
| E. Ameryka Południowa (czas standardowy) | (UTC-03:00) Brasilia |
| SA Wschodni czas standardowy | (UTC-03:00) Cayenne , Fortaleza |
| Argentyna (czas standardowy) | (UTC-03:00) City of Buenos Aires |
| Grenlandia (czas standardowy) | (UTC-03:00) Grenlandii |
| Montevideo Czas standardowy | (UTC-03:00) Montevideo |
| Magallanes Czas standardowy | (UTC-03:00) Hale widowisne Punta |
| Saint Pierre (czas standardowy) | (UTC-03:00) Św Pierre i Miquelon |
| Bahia (czas standardowy) | (UTC-03:00) Salvador |
| UTC-02 | (UTC-02:00) Skoordynowany czas uniwersalny-02 |
| Średni Atlantyk (czas standardowy) | (UTC-02:00) Środkowy Atlantyk - Stary |
| Azory (czas standardowy) | (UTC-01:00) Azory |
| Republika Zielonego Przylądka (czas standardowy) | (UTC-01:00) Cabo Verde Jest. |
| UTC | (UTC) Uniwersalny czas koordynowany |
| GMT Czas standardowy | (UTC+00:00) Dublin, Edynburg, Lizbona, Londyn |
| Czas standardowy Greenwich | (UTC+00:00) Monrovia, Reykjavik |
| W. Europa (czas standardowy) | (UTC+01:00) Amsterdam, Berlin, Berno, Rzym, Sztokholm, Wiedeń |
| Europa Środkowa (czas standardowy) | (UTC+01:00) Belgrad, Bratysława, Budapeszt, Lublana, Praga |
| Romans standardowy czas | (UTC+01:00) Bruksela, Kopenhaga, Madryt, Paryż |
| Maroko (czas standardowy) | (UTC+01:00) Casablanca |
| Sao Tome Czas standardowy | (UTC+01:00) Sao Tome |
| Środkowoeuropejski czas standardowy | (UTC+01:00) Sarajewo, Skopje, Warszawa, Zagrzeb |
| W. Afryka Środkowa (czas standardowy) | (UTC+01:00) Afryka Zachodnio-Środkowa |
| Jordan (czas standardowy) | (UTC+02:00) Amman |
| Gtb czas standardowy | (UTC+02:00) Ateny, Bukareszt |
| Bliski Wschód Czasu Standardowego | (UTC+02:00) Bejrut |
| Egipt (czas standardowy) | (UTC+02:00) Kair |
| E. Europa (czas standardowy) | (UTC+02:00) Chisinau |
| Syria (czas standardowy) | (UTC+02:00) Damascus |
| Zachodni bank standardowy czas | (UTC+02:00) Gaza, Hebron |
| Republika Południowej Afryki (czas standardowy) | (UTC+02:00) Harare, Pretoria |
| FLE Czas standardowy | (UTC+02:00) Helsinki, Kijów, Ryga, Sofia, Tallin, Wilno |
| Izrael (czas standardowy) | (UTC+02:00) Jerozolima |
| Kaliningrad (czas standardowy) | (UTC+02:00) Kaliningrad |
| Sudan (czas standardowy) | (UTC+02:00) Chartum |
| Libia Czas standardowy | (UTC+02:00) Trypolisie |
| Namibia (czas standardowy) | (UTC+02:00) Windhoek |
| Arabski czas standardowy | (UTC+03:00) Bagdadzie |
| Turcja (czas standardowy) | (UTC+03:00) Stambuł |
| Arabski czas standardowy | (UTC+03:00) Kuwejt, Rijad |
| Białoruś (czas standardowy) | (UTC+03:00) Mińsk |
| Rosyjski czas standardowy | (UTC+03:00) Moskwa, Sankt Petersburg |
| E. Afryka (czas standardowy) | (UTC+03:00) Nairobi |
| Iran (czas standardowy) | (UTC+03:30) Teheran |
| Arabski czas standardowy | (UTC+04:00) Abu Dhabi, Maskat |
| Astrachań czas standardowy | (UTC+04:00) Astrachań, Uljanowsk |
| Azerbejdżan (czas standardowy) | (UTC+04:00) Baku |
| Strefa Czasowa Rosji 3 | (UTC+04:00) Iżewewski, Samara |
| Mauritius (czas standardowy) | (UTC+04:00) Port Louis |
| Saratow (czas standardowy) | (UTC+04:00) Saratov |
| Gruziński czas standardowy | (UTC+04:00) Tbilisi |
| Volgograd (czas standardowy) | (UTC+04:00) Wołgograd |
| Czas standardowy Kaukazu | (UTC+04:00) Erewan |
| Afganistan (czas standardowy) | (UTC+04:30) Kabulu |
| Zachodnia Azja (czas standardowy) | (UTC+05:00) Aszchabad, Taszkient |
| Ekaterynburg (czas standardowy) | (UTC+05:00) Ekaterinburg |
| Pakistan (czas standardowy) | (UTC+05:00) Islamabad, Karaczi |
| Indie (czas standardowy) | (UTC+05:30) Chennai, Kalkuta, Bombaj, New Delhi |
| Sri Lanka (czas standardowy) | (UTC+05:30) Sri Jayawardenepura |
| Nepal (czas standardowy) | (UTC+05:45) Kathmandu |
| Azja Środkowa (czas standardowy) | (UTC+06:00) Astana |
| Bangladesz (czas standardowy) | (UTC+06:00) Dhaka |
| Omsk czas standardowy | (UTC+06:00) Omsk |
| Birma (czas standardowy) | (UTC+06:30) Rangun (Rangoon) |
| SE Azja Czas standardowy | (UTC+07:00) Bangkok, Hanoi, Dżakarta |
| Altai Czas standardowy | (UTC+07:00) Barnaul, Gorno-Altaysk |
| W. Mongolia (czas standardowy) | (UTC+07:00) Hovd ( Hovd ) |
| Azja Północna (czas standardowy) | (UTC+07:00) Krasnojarsk |
| N. Azja Środkowa (czas standardowy) | (UTC+07:00) Nowosybirsk |
| Tomsk Czas standardowy | (UTC+07:00) Tomsk |
| Chiny (czas standardowy) | (UTC+08:00) Pekin, Chongqing, Hongkong, Urumqi |
| Azja Północna Wschodnia (czas standardowy) | (UTC+08:00) Irkuck |
| Singapur (czas standardowy) | (UTC+08:00) Kuala Lumpur, Singapur |
| W. Australia (czas standardowy) | (UTC+08:00) Perth |
| Tajpej (czas standardowy) | (UTC+08:00) Taipei |
| Ułan Bator (czas standardowy) | (UTC+08:00) Ułan bator |
| Aus Central W. Czas standardowy | (UTC+08:45) Eucla ( Eucla ) |
| Transbaikal Czas standardowy | (UTC+09:00) Chita |
| Tokio (czas standardowy) | (UTC+09:00) Osaka, Sapporo, Tokio |
| Korea Północna (czas standardowy) | (UTC+09:00) Phenianie |
| Korea (czas standardowy) | (UTC+09:00) Seul |
| Jakuck (czas standardowy) | (UTC+09:00) Jakuck |
| Cen. Australia (czas standardowy) | (UTC+09:30) Adelaide |
| Centralny czas standardowy AUS | (UTC+09:30) Darwin |
| E. Australia (czas standardowy) | (UTC+10:00) Brisbane |
| AUS Wschodni czas standardowy | (UTC+10:00) Canberra, Melbourne, Sydney |
| Zachodni Pacyfik (czas standardowy) | (UTC+10:00) Guam, Port Moresby |
| Tasmania (czas standardowy) | (UTC+10:00) Hobart |
| Władywostok Standardowy Czas | (UTC+10:00) Władywostoku |
| Lord Howe Czas standardowy | (UTC+10:30) Wyspa Lorda Howe'a |
| Bougainville (czas standardowy) | (UTC+11:00) Wyspa Bougainville |
| Strefa Czasowa Rosji 10 | (UTC+11:00) Chokurdakh ( chokurdakh ) |
| Magadan (czas standardowy) | (UTC+11:00) Magadan |
| Norfolk (czas standardowy) | (UTC+11:00) Wyspa Norfolk |
| Sachalin Czas standardowy | (UTC+11:00) Sachalin |
| Centralny Pacyfik (czas standardowy) | (UTC+11:00) Solomon Is., Nowa Kaledonia |
| Strefa Czasowa Rosji 11 | (UTC+12:00) Anadyr, Petropavlovsk-Kamchatsky |
| Nowa Zelandia (czas standardowy) | (UTC+12:00) Auckland – |
| UTC+12 | (UTC+12:00) Skoordynowany czas uniwersalny+12 |
| Fidżi (czas standardowy) | (UTC+12:00) Fidżi |
| Kamczatka Czas standardowy | (UTC+12:00) Petropavlovsk-Kamchatsky - Stary |
| Chatham Islands Czas standardowy | (UTC+12:45) Wyspy Chatham |
| UTC+13 | (UTC+13:00) Skoordynowany czas uniwersalny+13 |
| Tonga Czas standardowy | (UTC+13:00) Nuku'alofa |
| Samoa Czas standardowy | (UTC+13:00) Samoa |
| Wyspy liniowe Czas standardowy | (UTC+14:00) Wyspa Kiritimati |

## <a name="see-also"></a>Zobacz też 

- [CURRENT_TIMEZONE (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/functions/current-timezone-transact-sql)
- [W STREFIE CZASOWEJ (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/queries/at-time-zone-transact-sql)
- [sys.time_zone_info (Transact-SQL)](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-time-zone-info-transact-sql)
