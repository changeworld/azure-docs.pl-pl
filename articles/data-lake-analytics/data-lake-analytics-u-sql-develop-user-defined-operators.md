---
title: Opracowywanie operatorów języka U-SQL zdefiniowane przez użytkownika (udo) w usłudze Azure Data Lake Analytics
description: Dowiedz się, jak tworzyć aplikacje zdefiniowane przez użytkownika operatory używanych i ponownie użyte w zadaniach usługi Azure Data Lake Analytics.
services: data-lake-analytics
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: e5189e4e-9438-46d1-8686-ed4836bf3356
ms.topic: conceptual
ms.date: 12/05/2016
ms.openlocfilehash: 122a4b6af78a22f74d5057da75767077f8d9b978
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60813795"
---
# <a name="develop-u-sql-user-defined-operators-udos"></a>Opracowywanie operatorów języka U-SQL zdefiniowane przez użytkownika (udo)
W tym artykule opisano tworzenie operatory zdefiniowane przez użytkownika do przetwarzania danych w ramach zadania U-SQL.

## <a name="define-and-use-a-user-defined-operator-in-u-sql"></a>Definiowanie i korzystanie z operatora zdefiniowanego przez użytkownika w języku U-SQL
**Tworzenie i przesyłanie zadania U-SQL**

1. Z programu Visual Studio wybierz opcję **Plik > Nowy > Projekt > Projekt U-SQL**.
2. Kliknij przycisk **OK**. Program Visual Studio tworzy rozwiązanie z plikiem Script.usql.
3. Z **Eksploratora rozwiązań**rozwiń Script.usql, a następnie kliknij dwukrotnie ikonę **Script.usql.cs**.
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
6. Otwórz **Script.usql**i wklej poniższy skrypt U-SQL:

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
10. Jeszcze nie nawiązano z subskrypcją platformy Azure, zostanie wyświetlony monit wprowadź swoje poświadczenia konta platformy Azure.
11. Kliknij przycisk **Submit** (Prześlij). Wyniki przesyłania i link do zadania są dostępne w oknie wyników po zakończeniu przesyłania.
12. Kliknij przycisk **Odśwież** przycisk, aby wyświetlić najbardziej aktualny stan zadania i odświeżyć ekran.

**Aby wyświetlić dane wyjściowe**

1. Z **Eksploratora serwera**, rozwiń węzeł **Azure**, rozwiń węzeł **usługi Data Lake Analytics**, rozwiń konto usługi Data Lake Analytics, rozwiń węzeł **kont magazynu**, kliknij prawym przyciskiem myszy magazyn domyślne, a następnie kliknij przycisk **Explorer**.
2. Rozwiń przykłady, rozwiń danych wyjściowych, a następnie kliknij dwukrotnie ikonę **Drivers.csv**.

## <a name="see-also"></a>Zobacz także
* [Rozszerzanie wyrażenia języka U-SQL z kodu użytkownika](/u-sql/concepts/extending-u-sql-expressions-with-user-code)
* [Użyj narzędzi Data Lake Tools for Visual Studio do tworzenia aplikacji w języku U-SQL](data-lake-analytics-data-lake-tools-get-started.md)
