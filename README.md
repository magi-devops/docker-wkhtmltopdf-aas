# docker-wkhtmltopdf-aas

wkhtmltopdf in a docker container as a web service.

This image is based on the 
[wkhtmltopdf container](https://registry.hub.docker.com/u/openlabs/docker-wkhtmltopdf/).

## Running the service

Run the container with docker run and binding the ports to the host.
The web service is exposed on port 80 in the container.

```sh
docker run -d -P openlabs/docker-wkhtmltopdf-aas
```

The container now runs as a daemon.

Find the port that the container is bound to:

```sh
docker port 071599a1373e 80
```

where 071599a1373e is the container SHA that docker assigned when
`docker run` was executed in the previous command.

Take a note of the public port number where docker binds to.

## Using the webservice

There are multiple ways to generate a PDF of HTML using the
service.

### Uploading a HTML file

This is a convenient way to use the service from command line
utilities like curl.

```sh
curl -X POST -vv -F 'file=@path/to/local/file.html' http://<docker-host>:<port>/ -o path/to/output/file.pdf -----------> convert html file to pfd


curl -X POST -vv -F 'site=google.com' http://217.76.48.198:49153/ -o /root/file.pdf  ---------> convert site to pfd
```

where:

* docker-host is the hostname or address of the docker host running the container
* port is the public port to which the container is bound to.

### JSON API

If you are planning on using this service in your application,
it might be more convenient to use the JSON API that the service
uses.

Here is an example using python requests:

```python
import json
import requests

url = 'http://<docker_host>:<port>/'
data = {
    'contents': open('/file/to/convert.html').read().encode('base64'),
}
headers = {
    'Content-Type': 'application/json',    # This is important
}
response = requests.post(url, data=json.dumps(data), headers=headers)

# Save the response contents to a file
with open('/path/to/local/file.pdf', 'wb') as f:
    f.write(response.content)
```

Here is another example in python, but this time we pass options to wkhtmltopdf.
When passing our settings we omit the double dash "--" at the start of the option.
For documentation on what options are available, visit http://wkhtmltopdf.org/usage/wkhtmltopdf.txt

```python
import json
import requests

url = 'http://<docker_host>:<port>/'
data = {
    'contents': open('/file/to/convert.html').read().encode('base64'),
    'options': {
        #Omitting the "--" at the start of the option
        'margin-top': '6', 
        'margin-left': '6', 
        'margin-right': '6', 
        'margin-bottom': '6', 
        'page-width': '105mm', 
        'page-height': '40mm'
    }
}
headers = {
    'Content-Type': 'application/json',    # This is important
}
response = requests.post(url, data=json.dumps(data), headers=headers)

# Save the response contents to a file
with open('/path/to/local/file.pdf', 'wb') as f:
    f.write(response.content)
```

## TODO

* Implement conversion of URLs to PDF
* Add documentation on passing options to the service
* Add `curl` example for JSON api
* Explain more gunicorn options

## Bugs and questions

The development of the container takes place on 
[Github](https://github.com/openlabs/docker-wkhtmltopdf-aas). If you
have a question or a bug report to file, you can report as a github issue.


## Authors and Contributors

This image was built at [Openlabs](http://www.openlabs.co.in).

## Professional Support

This image is professionally supported by [Openlabs](http://www.openlabs.co.in).
If you are looking for on-site teaching or consulting support, contact our
[sales](mailto:sales@openlabs.co.in) and [support](mailto:support@openlabs.co.in) teams.
