Setup for nest js


yarn add @nestjs/winston winston winston-elasticsearch


logger.config.ts

```
import { transports, format } from 'winston';
import ElasticsearchTransport from 'winston-elasticsearch';

const esTransportOptions = {
  level: 'info', // Уровень логирования
  clientOpts: {
    node: 'http://localhost:9200', // URL вашего Elasticsearch
    auth: {
      username: 'elastic', // user Elasticsearch
      password: 'changeme', // pass Elasticsearch
    },
  },
  indexPrefix: 'nestjs-logs', // Префикс индекса
};

export const loggerConfig = {
  transports: [
    new transports.Console({
      format: format.combine(format.colorize(), format.simple()),
    }),
    new ElasticsearchTransport(esTransportOptions),
  ],
};



```




main.ts

```
import { Logger } from '@nestjs/common';
import { NestFactory } from '@nestjs/core';
import { WinstonModule } from 'nest-winston';
import { AppModule } from './app.module';
import { loggerConfig } from './logger.config';

async function bootstrap() {
  const app = await NestFactory.create(AppModule, {
    logger: WinstonModule.createLogger(loggerConfig),
  });

  await app.listen(3000);
  Logger.log('Application is running on http://localhost:3000');
}
bootstrap();

```


nginx

```
sudo nano /etc/nginx/sites-available/kibana

```

```
server {
    listen 443 ssl;
    server_name your-domain.com;

    ssl_certificate /path/to/your/certificate.crt;
    ssl_certificate_key /path/to/your/certificate.key;

    location / {
        proxy_pass http://localhost:5601;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    }
}

server {
    listen 80;
    server_name your-domain.com;

    return 301 https://$host$request_uri;
}

```
```

sudo ln -s /etc/nginx/sites-available/kibana /etc/nginx/sites-enabled/


sudo systemctl restart nginx



```

