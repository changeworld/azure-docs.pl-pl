---
title: Tworzenie operatorów zdefiniowanych przez użytkownika U-SQL — usługa Azure Data Lake Analytics
description: Dowiedz się, jak tworzyć operatory zdefiniowane przez użytkownika, które będą używane i ponownie używane w zadaniach usługi Azure Data Lake Analytics.
services: data-lake-analytics
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: e5189e4e-9438-46d1-8686-ed4836bf3356
ms.topic: conceptual
ms.date: 12/05/2016
ms.openlocfilehash: b2d1293b06b4d8791138ed666bc3cb4abe3adf40
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "71316540"
---
# <a name="develop-u-sql-user-defined-operators-udos"></a>Tworzenie operatorów zdefiniowanych przez użytkownika U-SQL (UDO)
W tym artykule opisano sposób tworzenia operatorów zdefiniowanych przez użytkownika do przetwarzania danych w zadaniu U-SQL.

## <a name="define-and-use-a-user-defined-operator-in-u-sql"></a>Definiowanie i używanie operatora zdefiniowanego przez użytkownika w języku U-SQL
**Aby utworzyć i przesłać zadanie U-SQL**

1. Z programu Visual Studio wybierz **opcję Plik > nowy > project > programie U-SQL Project**.
2. Kliknij przycisk **OK**. Program Visual Studio tworzy rozwiązanie z plikiem Script.usql.
3. Z **Eksploratora rozwiązań**rozwiń pozycję Script.usql, a następnie kliknij dwukrotnie **Script.usql.cs**.
4. Wklej następujący kod do pliku:

        using Microsoft.Analytics.Interfaces;
        using System.Collections.Generic;

        namespace USQL_UDO
        {
            public class CountryName : IProcessor
            {
                private static IDictionary<string, string> CountryTranslation = new Dictionary<string, string>
                {
                    {
                        "Deutschland", "Germany"
                    },
                    {
                        "Suisse", "Switzerland"
                    },
                    {
                        "UK", "United Kingdom"
                    },
                    {
                        "USA", "United States of America"
                    },
                    {
                        "中国", "PR China"
                    }
                };

                public override IRow Process(IRow input, IUpdatableRow output)
                {

                    string UserID = input.Get<string>("UserID");
                    string Name = input.Get<string>("Name");
                    string Address = input.Get<string>("Address");
                    string City = input.Get<string>("City");
                    string State = input.Get<string>("State");
                    string PostalCode = input.Get<string>("PostalCode");
                    string Country = input.Get<string>("Country");
                    string Phone = input.Get<string>("Phone");

                    if (CountryTranslation.Keys.Contains(Country))
                    {
                        Country = CountryTranslation[Country];
                    }
                    output.Set<string>(0, UserID);
                    output.Set<string>(1, Name);
                    output.Set<string>(2, Address);
                    output.Set<string>(3, City);
                    output.Set<string>(4, State);
                    output.Set<string>(5, PostalCode);
                    output.Set<string>(6, Country);
                    output.Set<string>(7, Phone);

                    return output.AsReadOnly();
                }
            }
        }
6. Otwórz **plik Script.usql**i wklej następujący skrypt U-SQL:

        @drivers =
            EXTRACT UserID      string,
                    Name        string,
                    Address     string,
                    City        string,
                    State       string,
                    PostalCode  string,
                    Country     string,
                    Phone       string
            FROM "/Samples/Data/AmbulanceData/Drivers.txt"
            USING Extractors.Tsv(Encoding.Unicode);

        @drivers_CountryName =
            PROCESS @drivers
            PRODUCE UserID string,
                    Name string,
                    Address string,
                    City string,
                    State string,
                    PostalCode string,
                    Country string,
                    Phone string
            USING new USQL_UDO.CountryName();    

        OUTPUT @drivers_CountryName
            TO "/Samples/Outputs/Drivers.csv"
            USING Outputters.Csv(Encoding.Unicode);
7. Określ konto usługi Data Lake Analytics, bazę danych i schemat.
8. W **Eksploratorze rozwiązań** kliknij prawym przyciskiem myszy pozycję **Script.usql**, a następnie kliknij pozycję **Build Script** (Kompiluj skrypt).
9. W **Eksploratorze rozwiązań** kliknij prawym przyciskiem myszy pozycję **Script.usql**, a następnie kliknij pozycję **Submit Script** (Prześlij skrypt).
10. Jeśli nie masz połączenia z subskrypcją platformy Azure, zostanie wyświetlony monit o wprowadzenie poświadczeń konta platformy Azure.
11. Kliknij **przycisk Prześlij**. Wyniki przesyłania i łącze zadania są dostępne w oknie Wyniki po zakończeniu przesyłania.
12. Kliknij przycisk **Odśwież,** aby wyświetlić najnowszy stan zadania i odświeżyć ekran.

**Aby zobaczyć dane wyjściowe**

1. Z **Eksploratora serwera**rozwiń **pozycję Azure**, rozwiń usługę Data Lake **Analytics**, rozwiń konto usługi Data Lake Analytics, rozwiń pozycję **Konta magazynu**, kliknij prawym przyciskiem myszy magazyn domyślny, a następnie kliknij polecenie **Eksplorator**.
2. Rozwiń pozycję Przykłady, rozwiń pozycję Dane wyjściowe, a następnie kliknij dwukrotnie pozycję **Drivers.csv**.

## <a name="see-also"></a>Zobacz też
* [Rozszerzanie wyrażeń U-SQL za pomocą kodu użytkownika](/u-sql/concepts/extending-u-sql-expressions-with-user-code)
* [Używanie narzędzi usługi Data Lake Tools dla programu Visual Studio do tworzenia aplikacji U-SQL](data-lake-analytics-data-lake-tools-get-started.md)
