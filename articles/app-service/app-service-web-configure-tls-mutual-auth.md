---
title: Konfigurowanie wzajemnego uwierzytelniania protokołu TLS — usługa Azure App Service
description: Dowiedz się, jak skonfigurować aplikację do używania uwierzytelniania certyfikatu klienta na TLS.
services: app-service
documentationcenter: ''
author: naziml
manager: erikre
editor: jimbe
ms.assetid: cd1d15d3-2d9e-4502-9f11-a306dac4453a
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/08/2016
ms.author: naziml
ms.custom: seodec18
ms.openlocfilehash: d441329bc3f279e95b2ee302db53d78f786c3470
ms.sourcegitcommit: e68df5b9c04b11c8f24d616f4e687fe4e773253c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/20/2018
ms.locfileid: "53650401"
---
# <a name="how-to-configure-tls-mutual-authentication-for-azure-app-service"></a>Jak skonfigurować wzajemne uwierzytelnianie protokołu TLS dla usługi Azure App Service
## <a name="overview"></a>Przegląd
Możesz ograniczyć dostęp do aplikacji usługi Azure App Service, włączając różnego rodzaju uwierzytelniania dla niego. Jednym ze sposobów, w tym celu jest uwierzytelnianie przy użyciu certyfikatu klienta, gdy żądanie zostanie za pośrednictwem protokołu TLS/SSL. Mechanizm ten nosi nazwę wzajemnego uwierzytelniania protokołu TLS lub certyfikat klienta, że uwierzytelnianie i w tym artykule zostanie szczegółowo opisują jak skonfigurować aplikację, aby użyć uwierzytelniania certyfikatu klienta.

> **Uwaga:** Jeśli uzyskujesz dostęp do witryny za pośrednictwem protokołu HTTP, a nie HTTPS, nie otrzymasz żadnych certyfikatu klienta. Dlatego jeśli aplikacja wymaga certyfikatów klienta nie należy zezwalać żądania do aplikacji za pośrednictwem protokołu HTTP.
> 
> 

## <a name="configure-app-service-for-client-certificate-authentication"></a>Konfigurowanie usługi App Service do uwierzytelniania certyfikatu klienta
Aby skonfigurować aplikację, aby wymagać certyfikaty klienta, należy dodać ustawienie elementu clientCertEnabled witryny, aplikacji i ustaw ją na wartość true. To ustawienie jest również możliwe do skonfigurowania w witrynie Azure portal w obszarze blok certyfikaty SSL.

Możesz użyć [narzędzie ARMClient](https://github.com/projectkudu/ARMClient) ułatwia sformułować wywołania interfejsu API REST. Po zalogowaniu się przy użyciu narzędzia, należy wydać następujące polecenie:

    ARMClient PUT subscriptions/{Subscription Id}/resourcegroups/{Resource Group Name}/providers/Microsoft.Web/sites/{Website Name}?api-version=2015-04-01 @enableclientcert.json -verbose

zastąpienie wszystkich elementów w {} z informacjami o aplikacji i tworzenia pliku o nazwie zawartości enableclientcert.json następującym kodem JSON:

    {
        "location": "My App Location",
        "properties": {
            "clientCertEnabled": true
        }
    }

Upewnij się zmienić wartość "Lokalizacja" wszędzie tam, gdzie aplikacja znajduje się na przykład, północno-środkowe stany USA i zachodnie stany USA itp.

Można również użyć https://resources.azure.com przerzucić `clientCertEnabled` właściwość `true`.

> **Uwaga:** Jeśli uruchamiasz ARMClient za pomocą programu Powershell, konieczne będzie ucieczki \@ symboli dla pliku JSON z tyłu znaczników ".
> 
> 

## <a name="accessing-the-client-certificate-from-app-service"></a>Uzyskiwanie dostępu do certyfikatu klienta z usługi App Service
Jeśli używasz programu ASP.NET i skonfiguruj aplikację, aby użyć uwierzytelniania certyfikatu klienta, certyfikat będzie dostępna za pośrednictwem **HttpRequest.ClientCertificate** właściwości. Dla innych stosów aplikacji certyfikat klienta będzie dostępne w Twojej aplikacji za pomocą wartości zakodowane w formacie base64 w nagłówku żądania "X-ARR ClientCert". Aplikację można utworzyć certyfikat z tej wartości, a następnie użyć go do celów uwierzytelniania i autoryzacji w aplikacji.

## <a name="special-considerations-for-certificate-validation"></a>Specjalne uwagi dotyczące weryfikacji certyfikatów
Certyfikat klienta, które są wysyłane do aplikacji nie przechodzi przez wszystkie weryfikacji przez platformę Azure App Service. Sprawdzanie poprawności tego certyfikatu jest odpowiedzialny za aplikacji. Poniżej przedstawiono przykładowy kod platformy ASP.NET, która weryfikuje właściwości certyfikatu na potrzeby uwierzytelniania.

    using System;
    using System.Collections.Specialized;
    using System.Security.Cryptography.X509Certificates;
    using System.Web;

    namespace ClientCertificateUsageSample
    {
        public partial class cert : System.Web.UI.Page
        {
            public string certHeader = "";
            public string errorString = "";
            private X509Certificate2 certificate = null;
            public string certThumbprint = "";
            public string certSubject = "";
            public string certIssuer = "";
            public string certSignatureAlg = "";
            public string certIssueDate = "";
            public string certExpiryDate = "";
            public bool isValidCert = false;

            //
            // Read the certificate from the header into an X509Certificate2 object
            // Display properties of the certificate on the page
            //
            protected void Page_Load(object sender, EventArgs e)
            {
                NameValueCollection headers = base.Request.Headers;
                certHeader = headers["X-ARR-ClientCert"];
                if (!String.IsNullOrEmpty(certHeader))
                {
                    try
                    {
                        byte[] clientCertBytes = Convert.FromBase64String(certHeader);
                        certificate = new X509Certificate2(clientCertBytes);
                        certSubject = certificate.Subject;
                        certIssuer = certificate.Issuer;
                        certThumbprint = certificate.Thumbprint;
                        certSignatureAlg = certificate.SignatureAlgorithm.FriendlyName;
                        certIssueDate = certificate.NotBefore.ToShortDateString() + " " + certificate.NotBefore.ToShortTimeString();
                        certExpiryDate = certificate.NotAfter.ToShortDateString() + " " + certificate.NotAfter.ToShortTimeString();
                    }
                    catch (Exception ex)
                    {
                        errorString = ex.ToString();
                    }
                    finally 
                    {
                        isValidCert = IsValidClientCertificate();
                        if (!isValidCert) Response.StatusCode = 403;
                        else Response.StatusCode = 200;
                    }
                }
                else
                {
                    certHeader = "";
                }
            }

            //
            // This is a SAMPLE verification routine. Depending on your application logic and security requirements, 
            // you should modify this method
            //
            private bool IsValidClientCertificate()
            {
                // In this example we will only accept the certificate as a valid certificate if all the conditions below are met:
                // 1. The certificate is not expired and is active for the current time on server.
                // 2. The subject name of the certificate has the common name nildevecc
                // 3. The issuer name of the certificate has the common name nildevecc and organization name Microsoft Corp
                // 4. The thumbprint of the certificate is 30757A2E831977D8BD9C8496E4C99AB26CB9622B
                //
                // This example does NOT test that this certificate is chained to a Trusted Root Authority (or revoked) on the server 
                // and it allows for self signed certificates
                //

                if (certificate == null || !String.IsNullOrEmpty(errorString)) return false;

                // 1. Check time validity of certificate
                if (DateTime.Compare(DateTime.Now, certificate.NotBefore) < 0 || DateTime.Compare(DateTime.Now, certificate.NotAfter) > 0) return false;

                // 2. Check subject name of certificate
                bool foundSubject = false;
                string[] certSubjectData = certificate.Subject.Split(new char[] { ',' }, StringSplitOptions.RemoveEmptyEntries);
                foreach (string s in certSubjectData)
                {
                    if (String.Compare(s.Trim(), "CN=nildevecc") == 0)
                    {
                        foundSubject = true;
                        break;
                    }
                }
                if (!foundSubject) return false;

                // 3. Check issuer name of certificate
                bool foundIssuerCN = false, foundIssuerO = false;
                string[] certIssuerData = certificate.Issuer.Split(new char[] { ',' }, StringSplitOptions.RemoveEmptyEntries);
                foreach (string s in certIssuerData)
                {
                    if (String.Compare(s.Trim(), "CN=nildevecc") == 0)
                    {
                        foundIssuerCN = true;
                        if (foundIssuerO) break;
                    }

                    if (String.Compare(s.Trim(), "O=Microsoft Corp") == 0)
                    {
                        foundIssuerO = true;
                        if (foundIssuerCN) break;
                    }
                }

                if (!foundIssuerCN || !foundIssuerO) return false;

                // 4. Check thumprint of certificate
                if (String.Compare(certificate.Thumbprint.Trim().ToUpper(), "30757A2E831977D8BD9C8496E4C99AB26CB9622B") != 0) return false;

                // If you also want to test if the certificate chains to a Trusted Root Authority you can uncomment the code below
                //
                //X509Chain certChain = new X509Chain();
                //certChain.Build(certificate);
                //bool isValidCertChain = true;
                //foreach (X509ChainElement chElement in certChain.ChainElements)
                //{
                //    if (!chElement.Certificate.Verify())
                //    {
                //        isValidCertChain = false;
                //        break;
                //    }
                //}
                //if (!isValidCertChain) return false;

                return true;
            }
        }
    }
