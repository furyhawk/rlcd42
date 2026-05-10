idf_component_register(
    SRCS "user_app.cpp"
    PRIV_REQUIRES main ui_bsp esp_netif
    INCLUDE_DIRS "./")
