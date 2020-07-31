
https://www.avioconsulting.com/blog/mule-cloudhub-logging-custom-logger-and-elk
https://www.elastic.co/blog/using-the-elastic-stack-for-observability-of-mulesoft-cloudhub

Index is configurable in Logstash side, then you have to point your log4j to ship the logs to that index. This is the example index we’ve got in Splunk for Trustpower:

         <SplunkHttp name="splunk" url="https://splunkhec.tpower.net.nz" token="${sys:splunk.token}"
             sourcetype="log4j" index="mulesoft" disableCertificateValidation="false">
                 <PatternLayout pattern="%m" />
         </SplunkHttp>


But on your case, you have to use Socket Appender.
https://help.mulesoft.com/s/article/How-to-send-Mule-application-logging-entries-to-Elastic-Stack-ELK-using-Socket-Appender
