# Instalación de WordPress en Arquitectura de Tres Niveles con Ansible

# Estructura del repositorio 

```
.
├── README.md
├── templates
│   └── 000-default.conf
└── inventory
│   └── inventory
└── playbooks
│   ├── setup_load_balancer.yml
│   ├── install_lamp_frontend.yml
│   ├── install_lamp_backend.yml
│   ├── setup_nfs_server.yml
│   ├── setup_nfs_client.yml
│   ├── setup_letsencrypt_certificate.yml
│   ├── deploy_wordpress_backend.yml
│   └── deploy_wordpress_frontend.yml
└── vars
│   └── variables.yml
└── main.yml
```



## Requisitos Previos
- Cuenta en AWS con acceso a EC2.
- Configuración de una clave SSH para acceso a las instancias.
- Instalación de Ansible en una máquina de control.
- Scripts de Bash de la práctica 1.11 como referencia.

1. **Balanceador de carga**
2. **2 Front-end**
3. **Back-end**
4. **Servidor NFS**


# Inventory 

```
[frontend]
_ipfrontend_
_ipfrontend_

-- Tenemos un grupo individual para un frontend donde  ejecutaremos el deploy 

[frontend1]
_ipfrontend_

[backend]
_ipbackend_

[balanceador]
_ipbalanceador_

[nfs]
_ipnfs_
```


## Recordemos la practica_11
### Proceso de ejecucion 
- Ejecutaremos nuestros install_lamp 
- Base de datos 
- Servidor NFS
- Clientes NFS 
    - 2 frontends
- Balanceador y certificado 
- Worpress
    - 1 frontend 

# Instlacion Worpress

- Descargamos  la utilidad WP-CLI
- Codigo fuente de worpress 
- Archivo de configuracion 
- Instalamos worpress
- Damos permisos adecuados 
- Instalar y activar el tema Mindscape
- Instalar y activar el plugin
- Configuramos enlaces permanentes 
- Archivo .htaccess
- Agregar variable $_SERVER['HTTPS']

Explicaremos lo de la variable 

```
    - name: Asegurar que $_SERVER['HTTPS'] esté en wp-config.php
      lineinfile:
        path: /var/www/html/wp-config.php
        line: "$_SERVER['HTTPS'] = 'on';"
        state: present
        insertbefore: "^define\\( 'DB_NAME', 'wordpress' \\);"
```
- tenemos el modulo `lineinfile`: con etse podemos agregar una linea antes o despues en nuestro contenido 
    - `insertbefore`: estamos agregando nuestra linea "$_SERVER['HTTPS'] = 'on';" antes de "^define\\( 'DB_NAME', 'wordpress' \\);" 
        - `^` ya sabemos que con esto marcamos el inicio de linea 
        - Hemos agregado `\\` son caracteres de escape : son utilizados para tratar los caracteres especiales como () , en nuestro caso teniamos que tratarlo como texto literal. 

El apartado de Themes nos dio errores al queres instalar `Mindscape`
Estos deben ser instalados con sudo _en mi caso fue asi_ 

```yml
    - name: Instalar y activar el tema Mindscape
      command: wp theme install mindscape --activate --path=/var/www/html --allow-root
      become: yes
```

- Podemos ver nuestros theme asi:

```yaml
wp theme activate theme-name --path=/var/www/html --allow-root
```

Y nuestros plugin 
```
wp plugin activate plugin-name --path=/var/www/html --allow-root

```


# Comprobaciones 
Comprobamos que los plugin se instalen 

![alt text](image.png)

Que nuestras enlaces permanentes y el theme funcionen 

![alt text](image-1.png)

Nuestro wps-hide-login `sitioescondido` y Ingreso con mi base de datos 

![alt text](image-2.png)

Certificado 

![alt text](image-3.png)

No-ip 


![alt text](image-4.png)

Corresponde a la ip que le hemos puesto a nuetsro dominio 

![alt text](image-5.png)

# REFERENCIAS

- [Instalacion Ansible][1]
- [Modulos Ansibles][2]
- [Primeros pasos a Ansible][3] Written by Carlos Aparicio

 [1]: https://josejuansanchez.org/taller-ansible-aws/#_ejemplo_12
 [2]: https://docs.ansible.com/ansible/latest/module_plugin_guide/index.html
 [3]: https://blog.deiser.com/es/primeros-pasos-con-ansible
