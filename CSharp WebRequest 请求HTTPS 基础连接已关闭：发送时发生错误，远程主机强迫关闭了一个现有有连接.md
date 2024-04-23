```
internal static class SendRequest
    { private static string GetBody(Dictionary<string, string> senddata)
        { if (senddata == null)
            {
                senddata = new Dictionary<string, string>();
            }
            StringBuilder buffer = new StringBuilder(); int i = 0; foreach (string k in senddata.Keys)
            { if (i > 0)
                {
                    buffer.AppendFormat("&{0}={1}", k, senddata[k]);
                } else {
                    buffer.AppendFormat("{0}={1}", k, senddata[k]);
                }
                i++;
            } string bodys = buffer.ToString(); return bodys;
        } public static string Send(string url, Dictionary<string, string> headerdata, Dictionary<string, string> senddata)
        { return Send(url, "POST", headerdata, senddata, "application/x-www-form-urlencoded;charset=UTF-8");
        } public static string Send(string url, string method, Dictionary<string, string> headerdata, Dictionary<string, string> senddata)
        { return Send(url, method, headerdata, senddata, "application/x-www-form-urlencoded;charset=UTF-8");
        } public static string Send(string url, string method, Dictionary<string, string> headerdata, Dictionary<string, string> senddata, string contentType)
        { string bodys = GetBody(senddata); //new internalErrorLog(new Exception(), "SendBody:" + bodys);
            HttpWebRequest httpRequest = null;
            HttpWebResponse httpResponse = null; if (url.Contains("https://"))
            {
                 //ServicePointManager.SecurityProtocol = SecurityProtocolType.Tls;
                // 请求某些接口一直返回基础连接已关闭：发送时发生错误
                // 远程主机强迫关闭了一个现有的连接
                 
                 ServicePointManager.SecurityProtocol = (SecurityProtocolType)3072;
                ServicePointManager.Expect100Continue = false;
                ServicePointManager.ServerCertificateValidationCallback = new RemoteCertificateValidationCallback(CheckValidationResult);

                httpRequest = (HttpWebRequest)WebRequest.CreateDefault(new Uri(url));
                httpRequest.ProtocolVersion = HttpVersion.Version10;

            } else {
                httpRequest = (HttpWebRequest)WebRequest.Create(url);
            }
            httpRequest.Proxy = null;
            httpRequest.Method = method; foreach (string k in headerdata.Keys)
            {
                httpRequest.Headers.Add(k, headerdata[k]);
            } //new internalErrorLog(new Exception(), "SendHeader:" + Newtonsoft.Json.JsonConvert.SerializeObject(headerdata)); //根据API的要求，定义相对应的Content-Type
            httpRequest.ContentType = contentType; if (0 < bodys.Length)
            { byte[] data = Encoding.UTF8.GetBytes(bodys); using (Stream stream = httpRequest.GetRequestStream())
                {
                    stream.Write(data, 0, data.Length);
                }
            } try {
                httpRequest.ServicePoint.Expect100Continue = false;
                httpResponse = (HttpWebResponse)httpRequest.GetResponse();
                Stream st = httpResponse.GetResponseStream();
                StreamReader reader = new StreamReader(st, Encoding.GetEncoding("utf-8")); string json = reader.ReadToEnd(); return json;
            } catch (WebException ex)
            { new internalErrorLog(ex, "请求URL:" + url);
                httpResponse = (HttpWebResponse)ex.Response; return ex.Message;
            }
        } private static bool CheckValidationResult(object sender, X509Certificate certificate, X509Chain chain, SslPolicyErrors errors)
        { return true;
        } public static string SendGet(string url, Dictionary<string, string> headerdata)
        {
            HttpWebRequest httpRequest = null;
            HttpWebResponse httpResponse = null; if (url.Contains("https://"))
            {
                ServicePointManager.SecurityProtocol =(SecurityProtocolType)3072;
                ServicePointManager.Expect100Continue = false;
                ServicePointManager.ServerCertificateValidationCallback = new RemoteCertificateValidationCallback(CheckValidationResult);
                httpRequest = (HttpWebRequest)WebRequest.CreateDefault(new Uri(url));
                httpRequest.ProtocolVersion = HttpVersion.Version10;
            } else {
                httpRequest = (HttpWebRequest)WebRequest.Create(url);
            }
            httpRequest.Proxy = null;
            httpRequest.Method = "Get";
            httpRequest.ContentType = "application/x-www-form-urlencoded;charset=UTF-8"; foreach (string k in headerdata.Keys)
            {
                httpRequest.Headers.Add(k, headerdata[k]);
            } try {
                httpResponse = (HttpWebResponse)httpRequest.GetResponse();
                Stream st = httpResponse.GetResponseStream();
                StreamReader reader = new StreamReader(st, Encoding.GetEncoding("utf-8")); string json = reader.ReadToEnd(); return json;
            } catch (WebException ex)
            {
                httpResponse = (HttpWebResponse)ex.Response; return ex.Message;
            }
        } public static string SendJSON(string url, JObject objdata)
        { string jsondata = objdata.ToString(); return SendJSON(url, jsondata);
        } public static string SendJSON(string url, string jsondata)
        {
            HttpWebRequest httpRequest = null;
            HttpWebResponse httpResponse = null; if (url.Contains("https://"))
            {
                ServicePointManager.SecurityProtocol = SecurityProtocolType.Ssl3 | SecurityProtocolType.Tls;
                ServicePointManager.Expect100Continue = false;
                ServicePointManager.ServerCertificateValidationCallback = new RemoteCertificateValidationCallback(CheckValidationResult);
                httpRequest = (HttpWebRequest)WebRequest.CreateDefault(new Uri(url));
                httpRequest.ProtocolVersion = HttpVersion.Version10;
                httpRequest.KeepAlive = false;
                ServicePointManager.DefaultConnectionLimit = 500;
            } else {
                httpRequest = (HttpWebRequest)WebRequest.Create(url);
            }
            httpRequest.Proxy = null;
            httpRequest.Method = "POST";
            httpRequest.ContentType = "application/json"; //WebClient.UploadString("", jsondata);
            try {
                httpRequest.ServicePoint.Expect100Continue = false; byte[] buffer = Encoding.UTF8.GetBytes(jsondata); //new internalErrorLog(new Exception(jsondata));
                httpRequest.ContentLength = buffer.Length;
                httpRequest.GetRequestStream().Write(buffer, 0, buffer.Length);
                httpResponse = (HttpWebResponse)httpRequest.GetResponse();
                Stream st = httpResponse.GetResponseStream();
                StreamReader reader = new StreamReader(st, Encoding.GetEncoding("utf-8")); string json = reader.ReadToEnd(); return json;
            } catch (WebException ex)
            { new internalErrorLog(ex, "发送地址:" + url);
                httpResponse = (HttpWebResponse)ex.Response; return ex.Message;
            }
        } public static string SendJSON(string url, Dictionary<string, string> header, string jsondata)
        {
            HttpWebRequest httpRequest = null;
            HttpWebResponse httpResponse = null; if (url.Contains("https://"))
            {
                ServicePointManager.SecurityProtocol = SecurityProtocolType.Ssl3 | SecurityProtocolType.Tls;
                ServicePointManager.Expect100Continue = false;
                ServicePointManager.ServerCertificateValidationCallback = new RemoteCertificateValidationCallback(CheckValidationResult);
                httpRequest = (HttpWebRequest)WebRequest.CreateDefault(new Uri(url));
            } else {
                httpRequest = (HttpWebRequest)WebRequest.Create(url);
            }
            httpRequest.Method = "POST";
            httpRequest.ContentType = "application/json"; foreach (string k in header.Keys)
            {
                httpRequest.Headers.Add(k, header[k]);
            } //WebClient.UploadString("", jsondata);
            try {
                httpRequest.ServicePoint.Expect100Continue = false; byte[] buffer = Encoding.UTF8.GetBytes(jsondata); new internalErrorLog(new Exception(jsondata));
                httpRequest.ContentLength = buffer.Length;
                httpRequest.GetRequestStream().Write(buffer, 0, buffer.Length);
                httpResponse = (HttpWebResponse)httpRequest.GetResponse();
                Stream st = httpResponse.GetResponseStream();
                StreamReader reader = new StreamReader(st, Encoding.GetEncoding("utf-8")); string json = reader.ReadToEnd(); return json;
            } catch (WebException ex)
            {
                httpResponse = (HttpWebResponse)ex.Response; return ex.Message;
            }
        } public static string Submit(string url, Hashtable ht, string type)
        { return Submit(url, ht, type, Encoding.Default);
        } public static string Submit(string url, NameValueCollection ht, string type)
        { return Submit(url, ht, type, Encoding.Default);
        } public static string Submit(string url, Hashtable ht, string type, Encoding encoding)
        {
            IDictionaryEnumerator enumerator = ht.GetEnumerator();
            NameValueCollection keyValue = new NameValueCollection(); while (enumerator.MoveNext())
            {
                keyValue.Add(enumerator.Key.ToString(), (enumerator.Value == null) ? "" : enumerator.Value.ToString());
            } return Submit(url, keyValue, type, encoding);
        } private static string Submit(string url, NameValueCollection keyValue, string type, Encoding encoding)
        { string str = string.Empty;
            WebClient client = new WebClient(); try { byte[] bytes = client.UploadValues(url, type.ToString(), keyValue);
                str = encoding.GetString(bytes);
            } catch (Exception exception)
            { throw exception;
            } finally {
                client.Dispose();
            } return str;
        }
    }
```
