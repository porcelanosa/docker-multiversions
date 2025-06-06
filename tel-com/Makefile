NGINX_CONTAINER=nginx
SITES_ENABLED=/etc/nginx/sites-enabled/tel-com.local
SITES_AVAILABLE=/etc/nginx/sites-available

.PHONY: dev prod restart-nginx status

dev:
	docker exec -it $(NGINX_CONTAINER) ln -sf $(SITES_AVAILABLE)/tel-com.local-dev.conf $(SITES_ENABLED)
	$(MAKE) restart-nginx

prod:
	docker exec -it $(NGINX_CONTAINER) ln -sf $(SITES_AVAILABLE)/tel-com.local-prod.conf $(SITES_ENABLED)
	$(MAKE) restart-nginx

restart-nginx:
	docker exec -it $(NGINX_CONTAINER) nginx -t && docker exec -it $(NGINX_CONTAINER) nginx -s reload
	@echo "✅ Nginx перезапущен. Активный конфиг:"
	docker exec -it $(NGINX_CONTAINER) ls -l $(SITES_ENABLED)

status:
	docker exec -it $(NGINX_CONTAINER) ls -l $(SITES_ENABLED)

# make dev    # подключит dev-конфиг в контейнере
# make prod   # подключит продакшн-конфиг
# make status # покажет текущий симлинк
