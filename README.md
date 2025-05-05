import streamlit as st
import pandas as pd
import plotly.express as px

# Cargar datos
df = pd.read_csv('vehicles_us.csv')

# Título y descripción
st.title("Análisis Interactivo de Vehículos Usados en EE.UU.")
st.write("""
Esta aplicación permite explorar un conjunto de datos de vehículos usados en EE.UU., 
visualizando relaciones clave entre precio, kilometraje, año y tipo de combustible.
""")

# Sidebar para filtros
st.sidebar.header("Filtros de Vehículos")
min_year = int(df['year'].min())
max_year = int(df['year'].max())
year_filter = st.sidebar.slider(
    "Año de fabricación", min_year, max_year, (min_year, max_year))

fuel_types = df['fuel'].dropna().unique().tolist()
selected_fuels = st.sidebar.multiselect(
    "Tipo de combustible", fuel_types, default=fuel_types)

# Filtrar datos según selección
df_filtered = df[(df['year'] >= year_filter[0]) & (
    df['year'] <= year_filter[1]) & (df['fuel'].isin(selected_fuels))]

# Mostrar dataframe filtrado opcionalmente
if st.sidebar.checkbox("Mostrar datos filtrados"):
    st.subheader("Datos filtrados")
    st.dataframe(df_filtered)

# Casillas de verificación para gráficos
st.subheader("Visualizaciones disponibles")
show_scatter = st.checkbox(
    "Mostrar gráfico de dispersión: Precio vs Kilometraje")
show_hist_price = st.checkbox("Mostrar histograma de precios")
show_hist_odo = st.checkbox("Mostrar histograma de kilometraje")
show_scatter_year = st.checkbox(
    "Mostrar gráfico de dispersión: Precio vs Año de fabricación")

# Mostrar gráfico scatter Precio vs Kilometraje
if show_scatter:
    st.subheader("Relación entre Precio y Kilometraje")
    fig_scatter = px.scatter(
        df_filtered, x="odometer", y="price",
        color="fuel",
        labels={"odometer": "Kilometraje (millas)", "price": "Precio (USD)"},
        title="Precio vs Kilometraje por Tipo de Combustible",
        color_discrete_sequence=px.colors.qualitative.Pastel,
        hover_data=['year', 'manufacturer', 'model']
    )
    st.plotly_chart(fig_scatter, use_container_width=True)

# Mostrar histograma de precios
if show_hist_price:
    st.subheader("Histograma de Precios de Vehículos Usados")
    fig_hist_price = px.histogram(
        df_filtered, x="price",
        nbins=50,
        title="Distribución de Precios",
        color_discrete_sequence=["#636EFA"],
        labels={"price": "Precio (USD)"}
    )
    st.plotly_chart(fig_hist_price, use_container_width=True)

# Mostrar histograma de kilometraje con control de bins
if show_hist_odo:
    st.subheader("Histograma de Kilometraje")
    bins = st.slider(
        "Número de bins para histograma de kilometraje", 10, 100, 30)
    fig_hist_odo = px.histogram(
        df_filtered, x="odometer",
        nbins=bins,
        title="Distribución de Kilometraje",
        color_discrete_sequence=["#EF553B"],
        labels={"odometer": "Kilometraje (millas)"}
    )
    st.plotly_chart(fig_hist_odo, use_container_width=True)

# Mostrar gráfico scatter Precio vs Año
if show_scatter_year:
    st.subheader("Relación entre Precio y Año de Fabricación")
    fig_year = px.scatter(
        df_filtered, x="year", y="price",
        color="fuel",
        labels={"year": "Año de Fabricación", "price": "Precio (USD)"},
        title="Precio vs Año de Fabricación por Tipo de Combustible",
        color_discrete_sequence=px.colors.qualitative.Vivid,
        hover_data=['odometer', 'manufacturer', 'model']
    )
    st.plotly_chart(fig_year, use_container_width=True)

# Estadísticas descriptivas básicas
st.subheader("Estadísticas Descriptivas")
st.write(df_filtered[['price', 'odometer', 'year']].describe())

# Información adicional
st.markdown("""
---
**Nota:** Puedes usar los filtros en la barra lateral para explorar diferentes subconjuntos del conjunto de datos.  
Selecciona una o varias visualizaciones para mostrarlas en la página principal.
""")
st.markdown("**Desarrollado por:** Juan Pablo García Chavez, 2025")
