 FROM debian:jessie
 MAINTAINER Fran Mendoza <software@globalstd.com>
  
 # Install some deps, lessc and less-plugin-clean-css, and wkhtmltopdf
 RUN set -x; \
        apt-get update \
        && apt-get install -y --no-install-recommends \
           ca-certificates \
           curl \
           git \
           node-less \
           node-clean-css \
           python-pyinotify \
           python-renderpm \
           python-support \
         && curl -o wkhtmltox.deb -SL http://nightly.odoo.com/extra/wkhtmltox-0.12.1.2_linux-jessie-amd64.deb \
         && echo '40e8b906de658a2221b15e4e8cd82565a47d7ee8 wkhtmltox.deb' | sha1sum -c - \
         && dpkg --force-depends -i wkhtmltox.deb \
         && apt-get -y install -f --no-install-recommends \
         && apt-get purge -y --auto-remove -o APT::AutoRemove::RecommendsImportant=false -o APT::AutoRemove::SuggestsImportant=false npm \
         && rm -rf /var/lib/apt/lists/* wkhtmltox.deb
  
  # Install Odoo
  ENV ODOO_VERSION 9.0
  ENV ODOO_RELEASE 20151215
  RUN set -x; \
          curl -o odoo.deb -SL http://nightly.odoo.com/${ODOO_VERSION}/nightly/deb/odoo_${ODOO_VERSION}c.${ODOO_RELEASE}_all.deb \
          && echo 'd3a246d44ff530cdfd494369030c1f095f00eb91 odoo.deb' | sha1sum -c - \
          && dpkg --force-depends -i odoo.deb \
          && apt-get update \
          && apt-get -y install -f --no-install-recommends \
         && rm -rf /var/lib/apt/lists/* odoo.deb
 
 # Copy entrypoint script and Odoo configuration file
 COPY ./entrypoint.sh /
 COPY ./openerp-server.conf /etc/odoo/
 RUN chown odoo /etc/odoo/openerp-server.conf && chmod +x /entrypoint.sh
  
 # Mount /var/lib/odoo to allow restoring filestore and /mnt/extra-addons for users addons
 RUN mkdir -p /mnt/extra-addons \
          && chown -R odoo /mnt/extra-addons
          
 # Clone globalstd-odoo-addons
 RUN git clone https://github.com/franktelum/globalstd-odoo-addons.git /mnt/extra-addons
  
 VOLUME ["/var/lib/odoo", "/mnt/extra-addons"]
 
 # Expose Odoo services
 EXPOSE 8069 8071
 
 # Set the default config file
 ENV OPENERP_SERVER /etc/odoo/openerp-server.conf
 
 # Set default user when running the container
 USER odoo
 
 ENTRYPOINT ["/entrypoint.sh"]
 CMD ["openerp-server"]
