---
title: Usługa Azure stref czasowych w wystąpieniu zarządzanym bazy danych SQL | Dokumentacja firmy Microsoft"
description: Dowiedz się więcej o charakterystyce strefę czasową dla wystąpienia zarządzanego Azure SQL Database
services: sql-database
ms.service: sql-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MladjoA
ms.author: mlandzic
ms.reviewer: ''
manager: craigg
ms.date: 05/22/2019
ms.openlocfilehash: 8499d99ab82fa89062d74c7dc5db5d7dd11e770c
ms.sourcegitcommit: db3fe303b251c92e94072b160e546cec15361c2c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/22/2019
ms.locfileid: "66016389"
---
# <a name="time-zones-in-azure-sql-database-managed-instance"></a>Strefy czasowe w wystąpieniu zarządzanym usługi Azure SQL Database

Uniwersalny czas koordynowany (UTC) jest zalecane strefę czasową dla rozwiązań w chmurze w warstwie danych. Wystąpienie usługi Azure SQL Database Managed oferuje szeroki wybór stref czasowych do potrzeb istniejących aplikacji, które przechowują wartości daty i godziny i wywołania funkcji daty i godziny przy użyciu niejawnego kontekstu określonej strefy czasowej.

Funkcje języka T-SQL, takie jak [GETDATE()](https://docs.microsoft.com/sql/t-sql/functions/getdate-transact-sql) lub kodu CLR Zwróć strefy czasowej ustawiony na wystąpieniu poziomu. Zadania agenta programu SQL Server wykonaj również harmonogramów zgodnie ze strefą czasową wystąpienia.

  >[!NOTE]
  > Wystąpienie zarządzane to opcja tylko wdrożenia usługi Azure SQL Database, która obsługuje ustawienie strefy czasowej. Inne opcje wdrażania zawsze postępuj zgodnie z czasem UTC.
Użyj [AT TIME ZONE](https://docs.microsoft.com/sql/t-sql/queries/at-time-zone-transact-sql) w jednej puli SQL bazach danych i jeśli należy interpretować informacje Data i godzina w strefie czasowej UTC bez.

## <a name="supported-time-zones"></a>Obsługiwane stref czasowych

Zestaw obsługiwanych stref czasowych są dziedziczone z system operacyjny wystąpienia zarządzanego. Jest regularnie aktualizowana, Pobierz nowe definicje strefy czasowej i uwzględnia zmiany w już istniejące. 

Listę z nazwami obsługiwanych stref czasowych jest dostępna za pośrednictwem [sys.time_zone_info](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-time-zone-info-transact-sql) widoku systemu.

## <a name="set-a-time-zone"></a>Ustawienie strefy czasowej

Podczas tworzenia wystąpienia tylko można skonfigurować strefę czasową dla wystąpienia zarządzanego. Domyślna strefa czasowa jest czasem UTC.

  >[!NOTE]
  > Nie można zmienić strefę czasową istniejącego wystąpienia zarządzanego.

### <a name="set-the-time-zone-through-the-azure-portal"></a>Ustaw strefę czasową w witrynie Azure portal

Po wprowadzeniu parametrów dla nowego wystąpienia, wybierz strefę czasową na liście obsługiwanych stref czasowych. 
  
![Ustawienie strefy czasowej podczas tworzenia wystąpienia](media/sql-database-managed-instance-timezone/01-setting_timezone-during-instance-creation.png)

### <a name="azure-resource-manager-template"></a>Szablon usługi Azure Resource Manager

Określa właściwości identyfikator strefy czasowej w swojej [szablonu usługi Resource Manager](https://aka.ms/sql-mi-create-arm-posh) konieczne ustawienie strefy czasowej podczas tworzenia wystąpienia.

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

Listę obsługiwanych wartości dla właściwości identyfikator strefy czasowej znajduje się na końcu tego artykułu.

Jeśli nie zostanie określony, strefa czasowa jest ustawiana na czas UTC.

## <a name="check-the-time-zone-of-an-instance"></a>Sprawdź strefę czasową wystąpienia

[CURRENT_TIMEZONE](https://docs.microsoft.com/sql/t-sql/functions/current-timezone-transact-sql) funkcja zwraca nazwę wyświetlaną wystąpienia strefy czasowej.

## <a name="cross-feature-considerations"></a>Zagadnienia związane z wielu funkcji

### <a name="restore-and-import"></a>Przywracanie i importowanie

Można przywrócić pliku kopii zapasowej lub importowanie danych do wystąpienia zarządzanego z wystąpienia usługi lub serwera z ustawieniami w innej strefie czasowej. Upewnij się to zrobić z ostrożnością. Sposób działania aplikacji i analizować wyniki kwerendy i raporty, podobnie jak podczas przesyłania danych między dwoma wystąpieniami programu SQL Server przy użyciu ustawień w innej strefie czasowej.

### <a name="point-in-time-restore"></a>Przywracanie do określonego momentu

Podczas wykonywania przywracania w momencie czasu, aby przywrócić jest interpretowany jako czas UTC. To ustawienie pozwala uniknąć niejednoznaczności z powodu zmiany czasu i potencjalne zmiany.

### <a name="auto-failover-groups"></a>Grupy automatycznego trybu failover

Przy użyciu tej samej strefie czasowej dla podstawowego i pomocniczego wystąpienie w grupie trybu failover nie są wymuszane, ale zdecydowanie zaleca się jej.

  >[!WARNING]
  > Zdecydowanie zaleca się, że używasz tej samej strefie czasowej dla wystąpienia podstawowych i pomocniczych w grupie trybu failover. Ze względu na kilka rzadkich scenariuszach zachowanie tej samej strefie czasowej w wystąpieniach podstawowego i pomocniczego nie są wymuszane. Należy zrozumieć w przypadku ręcznego lub automatycznego przejścia w tryb failover dodatkowej wystąpienia zostaną zachowane jego oryginalnej strefy czasowej.

## <a name="limitations"></a>Ograniczenia

- Nie można zmienić strefę czasową istniejącego wystąpienia zarządzanego.
- Zewnętrzne procesy uruchamiane z zadania agenta serwera SQL nie obowiązują strefy czasowej wystąpienia.

## <a name="list-of-supported-time-zones"></a>Listę obsługiwanych stref czasowych

| **Identyfikator strefy czasowej** | **Nazwa wyświetlana strefy czasowej** |
| --- | --- |
| Czas standardowy linii zmiany czasu | (UTC-12:00) Międzynarodowa linia zmiany daty — Zachód |
| UTC-11 | (UTC-11:00) Uniwersalny czas koordynowany-11 |
| Aleuty (czas standardowy) | (UTC-10:00) Aleuty |
| Hawaje (czas standardowy) | (UTC-10:00) Hawaje |
| Markizy (czas standardowy) | (UTC-09:30) Markizy |
| Alaska (czas standardowy) | (UTC-09:00) Alaska |
| UTC-09 | (UTC-09:00) Uniwersalny czas koordynowany-09 |
| Meksyk (standardowy czas pacyficzny) | (UTC-08:00) Kalifornia Dolna |
| UTC-08 | (UTC-08:00) Uniwersalny czas koordynowany-08 |
| Pacyfik (czas standardowy) | (UTC-08:00) Stany Zjednoczone i Kanada (czas pacyficzny) |
| Stany Zjednoczone (górski czas standardowy) | (UTC-07:00) Arizona |
| Górski czas standardowy (Meksyk) | (UTC-07:00) Chihuahua, La Paz, Mazatlan |
| Górski czas standardowy | (UTC-07:00) Stany Zjednoczone i Kanada (czas górski) |
| Ameryka Środkowa (czas standardowy) | (UTC-06:00) Ameryka Środkowa |
| Środkowy czas standardowy | (UTC-06:00) Stany Zjednoczone i Kanada (czas środkowy) |
| Wyspa Wielkanocna (czas standardowy) | (UTC-06:00) Wyspa Wielkanocna |
| Środkowy czas standardowy (Meksyk) | (UTC-06:00) Guadalajara, Meksyk, Monterrey |
| Kanada (środkowy czas stand.) | (UTC-06:00) Saskatchewan |
| Ameryka Południowa (pacyficzny czas standardowy) | (UTC-05:00) Bogota, Lima, Quito, Rio Branco |
| Wschodni czas standardowy (Meksyk) | (UTC-05:00) Chetumal |
| Wschodni czas standardowy | (UTC-05:00) Stany Zjednoczone i Kanada (czas wschodni) |
| Haiti (czas standardowy) | (UTC-05:00) Haiti |
| Kuba (czas standardowy) | (UTC-05:00) Hawana |
| Stany Zjednoczone (wschodni czas standardowy) | (UTC-05:00) Indiana (Wschód) |
| Wyspy Turks i Caicos (czas standardowy) | (UTC-05:00) Turks i Caicos |
| Paragwaj (czas standardowy) | (UTC-04:00) Asuncion |
| Atlantycki czas standardowy | (UTC-04:00) Kanada (czas atlantycki) |
| Wenezuela (czas standardowy) | (UTC-04:00) Caracas |
| Brazylia Środkowa (czas standardowy) | (UTC-04:00) Cuiaba |
| Ameryka Południowa (zachodni czas standardowy) | (UTC-04:00) Georgetown, La Paz, Manaus, San Juan |
| Standardowy czas pacyficzny (Ameryka Południowa) | (UTC-04:00) Santiago |
| Nowa Fundlandia (czas standardowy) | (UTC-03:30) Nowa Fundlandia |
| Tocantins (czas standardowy) | (UTC-03:00) Araguaina |
| E. Ameryka Południowa (czas standardowy) | (UTC-03:00) Brasilia |
| Ameryka Południowa (wschodni czas standardowy) | (UTC-03:00) Kajenna, Fortaleza |
| Argentyna (czas standardowy) | (UTC-03:00) Buenos Aires |
| Grenlandia (czas standardowy) | (UTC-03:00) Grenlandia |
| Montevideo (czas standardowy) | (UTC-03:00) Montevideo |
| Czas standardowy (Magallanes) | (UTC-03:00) Punta Arenas |
| Saint-Pierre (czas standardowy) | (UTC-03:00) Saint-Pierre i Miquelon |
| Bahia (czas standardowy) | (UTC-03:00) Salwador |
| UTC-02 | (UTC-02:00) Uniwersalny czas koordynowany-02 |
| Środkowy Atlantyk (czas standardowy) | (UTC-02:00) Środkowy Atlantyk — stare |
| Azory (czas standardowy) | (UTC-01:00) Azory |
| Wyspy Zielonego Przylądka (czas standardowy) | (UTC-01:00) Wyspy Zielonego Przylądka |
| UTC | (UTC) Uniwersalny czas koordynowany |
| GMT (czas standardowy) | (UTC+00:00) Dublin, Edynburg, Lizbona, Londyn |
| Greenwich (czas standardowy) | (UTC+00:00) Monrowia, Reykjavik |
| W. Europa (czas standardowy) | (UTC+01:00) Amsterdam, Berlin, Berno, Rzym, Sztokholm, Wiedeń |
| Europa Środkowa (czas standardowy) | (UTC+01:00) Belgrad, Bratysława, Budapeszt, Lublana, Praga |
| Romański czas standardowy | (UTC+01:00) Bruksela, Kopenhaga, Madryt, Paryż |
| Maroko (czas standardowy) | (UTC+01:00) Casablanca |
| Wyspa Świętego Tomasza — czas standardowy | (UTC+01:00) Wyspa Świętego Tomasza |
| Środkowoeuropejski czas standardowy | (UTC+01:00) Sarajewo, Skopie, Warszawa, Zagrzeb |
| W. Afryka Środkowozachodnia (czas standardowy) | (UTC+01:00) Afryka Środkowozachodnia |
| Jordania (czas standardowy) | (UTC+02:00) Amman |
| GTB (czas standardowy) | (UTC+02:00) Ateny, Bukareszt |
| Bliski Wschód (czas standardowy) | (UTC+02:00) Bejrut |
| Egipt (czas standardowy) | (UTC+02:00) Kair |
| E. Europa (czas standardowy) | (UTC+02:00) Kiszyniów |
| Syria (czas standardowy) | (UTC+02:00) Damaszek |
| Zachodni Brzeg (czas standardowy) | (UTC+02:00) Gaza, Hebron |
| Rep. Płd. Afryki (czas standardowy) | (UTC+02:00) Harare, Pretoria |
| Finlandia (czas standardowy) | (UTC+02:00) Helsinki, Kijów, Ryga, Sofia, Tallin, Wilno |
| Izrael (czas standardowy) | (UTC+02:00) Jerozolima |
| Kaliningrad (czas standardowy) | (UTC+02:00) Kaliningrad |
| Sudan (czas standardowy) | (UTC+02:00) Chartum |
| Libia (czas standardowy) | (UTC+02:00) Trypolis |
| Namibia (czas standardowy) | (UTC+02:00) Windhoek |
| Arabski czas standardowy | (UTC+03:00) Bagdad |
| Turcja (czas standardowy) | (UTC+03:00) Stambuł |
| Arabia Saud. (czas standardowy) | (UTC+03:00) Kuwejt, Ar-Rijad |
| Białoruś (czas standardowy) | (UTC+03:00) Mińsk |
| Rosyjski (czas standardowy) | (UTC+03:00) Moskwa, Petersburg |
| E. Afryki (czas standardowy) | (UTC+03:00) Nairobi |
| Iran (czas standardowy) | (UTC+03:30) Teheran |
| Arabia (czas standardowy) | (UTC+04:00) Abu Zabi, Maskat |
| Astrachań (czas standardowy) | (UTC+04:00) Astrachań, Uljanowsk |
| Azerbejdżan (czas standardowy) | (UTC+04:00) Baku |
| Russia Time Zone 3 | (UTC+04:00) Iżewsk, Samara |
| Mauritius (czas standardowy) | (UTC+04:00) Port Louis |
| Czas standardowy (Saratów) | (UTC+04:00) Saratów |
| Gruzja (czas standardowy) | (UTC+04:00) Tibilisi |
| Wołgograd (czas standardowy) | (UTC+04:00) Wołgograd |
| Kaukaz (czas standardowy) | (UTC+04:00) Erywań |
| Afganistan (czas standardowy) | (UTC+04:30) Kabul |
| Azja Zach. (czas standardowy) | (UTC+05:00) Aszchabad, Taszkient |
| Jekaterynburg (czas standardowy) | (UTC+05:00) Jekaterynburg |
| Pakistan (czas standardowy) | (UTC+05:00) Islamabad, Karaczi |
| Indie (czas standardowy) | (UTC+05:30) Chennai, Kolkata (Kalkuta), Mumbaj (Bombaj), Nowe Delhi |
| Sri Lanka (czas standardowy) | (UTC+05:30) Sri Dźajawardanapura Kotte |
| Nepal (czas standardowy) | (UTC+05:45) Katmandu |
| Azja Środkowa (czas standardowy) | (UTC+06:00) Astana |
| Bangladesz (czas standardowy) | (UTC+06:00) Dakka |
| Omsk (czas standardowy) | (UTC+06:00) Omsk |
| Mjanma (czas standardowy) | (UTC+06:30) Yangon (Rangun) |
| Azja Południowo-Wschodnia (czas standardowy) | (UTC+07:00) Bangkok, Hanoi, Dżakarta |
| Ałtaj (czas standardowy) | (UTC+07:00) Barnauł, Gornoałtajsk |
| W. Mongolia (czas standardowy) | (UTC+07:00) Ajmak kobdoski |
| Azja Północna (czas standardowy) | (UTC+07:00) Krasnojarsk |
| N. Azja Środkowa (czas standardowy) | (UTC+07:00) Nowosybirsk |
| Tomsk (czas standardowy) | (UTC+07:00) Tomsk |
| Chiny (czas standardowy) | (UTC+08:00) Pekin, Czungking, SRA Hongkong, Urumczi |
| Wschodnia Azja Północna (czas standardowy) | (UTC+08:00) Irkuck |
| Singapur (czas standardowy) | (UTC+08:00) Kuala Lumpur, Singapur |
| W. Australia (czas standardowy) | (UTC+08:00) Perth |
| Tajpej (czas standardowy) | (UTC+08:00) Tajpej |
| Ułan Bator (czas standardowy) | (UTC+08:00) Ułan Bator |
| Australia Środkowo-Zachodnia (czas standardowy) | (UTC+08:45) Eucla |
| Zabajkale (czas standardowy) | (UTC+09:00) Czyta |
| Tokio (czas standardowy) | (UTC+09:00) Osaka, Sapporo, Tokio |
| Korea Północna (czas standardowy) | (UTC+09:00) Phenian |
| Korea (czas standardowy) | (UTC+09:00) Seul |
| Jakuck (czas standardowy) | (UTC+09:00) Jakuck |
| Cen. Australia (czas standardowy) | (UTC+09:30) Adelajda |
| Australia (środkowy czas standardowy) | (UTC+09:30) Darwin |
| E. Australia (czas standardowy) | (UTC+10:00) Brisbane |
| Australia (wschodni czas standardowy) | (UTC+10:00) Canberra, Melbourne, Sydney |
| Zachodni Pacyfik (czas standardowy) | (UTC+10:00) Guam, Port Moresby |
| Tasmania (czas standardowy) | (UTC+10:00) Hobart |
| Władywostok (czas standardowy) | (UTC+10:00) Władywostok |
| Lord Howe (czas standardowy) | (UTC+10:30) Lord Howe |
| Bougainville (czas standardowy) | (UTC+11:00) Wyspa Bougainville’a |
| Russia Time Zone 10 | (UTC+11:00) Czokurdach |
| Magadan (czas standardowy) | (UTC+11:00) Magadan |
| Norfolk (czas standardowy) | (UTC+11:00) Norfolk |
| Sachalin (czas standardowy) | (UTC+11:00) Sachalin |
| Środkowy Pacyfik (czas standardowy) | (UTC+11:00) Wyspy Salomona, Nowa Kaledonia |
| Russia Time Zone 11 | (UTC+12:00) Anadyr, Pietropawłowsk Kamczacki |
| Nowa Zelandia (czas standardowy) | (UTC+12:00) Auckland, Wellington |
| UTC+12 | (UTC+12:00) Uniwersalny czas koordynowany+12 |
| Fidżi (czas standardowy) | (UTC+12:00) Fidżi |
| Kamczatka (czas standardowy) | (GMT+12:00) Pietropawłowsk Kamczacki — stare |
| Wyspy Chatham (czas standardowy) | (UTC+12:45) Wyspy Chatham |
| UTC+13 | (UTC+13:00) Uniwersalny czas koordynowany+13 |
| Tonga (czas standardowy) | (UTC+13:00) Nuku'alofa |
| Samoa (czas standardowy) | (UTC+13:00) Samoa |
| Line Islands (czas standardowy) | (UTC+14:00) Wyspa Kiritimati |

## <a name="see-also"></a>Zobacz także 

- [CURRENT_TIMEZONE (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/functions/current-timezone-transact-sql)
- [AT TIME ZONE (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/queries/at-time-zone-transact-sql)
- [sys.time_zone_info (Transact-SQL)](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-time-zone-info-transact-sql)
