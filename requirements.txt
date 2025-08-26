import streamlit as st
import pandas as pd
import random

# 🍪 Title
st.set_page_config(page_title="KosFinder 😎", page_icon="🏠")
st.title("🏠 KosFinder 😎")
st.caption("Cari kos biar gak nyasar ke hati mantan 💔")

# 🎯 Fun greeting
greetings = [
    "Halo pejuang tempat tinggal! 🏃‍♂️",
    "Lagi nyari kos? Jangan sampai dapet yang horor! 👻",
    "Semoga dapet kos yang gak pelit air 😅",
    "Kos yang bagus itu... ada kamu di dalamnya 😘"
]
st.subheader(random.choice(greetings))

# 📁 Load data
@st.cache_data
def load_data():
    return pd.read_csv("kos_data.csv")

data = load_data()

# 🎛️ Filter
st.sidebar.header("🔍 Filter pencarian kos")
lokasi = st.sidebar.multiselect("📍 Pilih Lokasi", options=data['lokasi'].unique(), default=data['lokasi'].unique())
harga_max = st.sidebar.slider("💸 Harga Maksimal (Rp)", min_value=500000, max_value=3000000, value=1500000, step=50000)

filtered_data = data[(data['lokasi'].isin(lokasi)) & (data['harga'] <= harga_max)]

# 🪄 Tampilkan hasil
st.subheader("✨ Daftar Kos Sesuai Pencarianmu:")
if filtered_data.empty:
    st.warning("Yah, gak ada kos yang cocok... 😭 Coba longgarkan filter-nya ya!")
else:
    for index, row in filtered_data.iterrows():
        st.markdown(f"""
        ---
        ### 🏡 {row['nama']}
        📍 **Lokasi:** {row['lokasi']}  
        💰 **Harga:** Rp {row['harga']:,}  
        🎁 **Fasilitas:** {row['fasilitas']}  
        """)
        st.markdown("😎 Cek langsung sebelum disikat orang!")

# ➕ Tambah kos baru
st.markdown("---")
st.subheader("🚀 Tambah Kos Baru (buat kamu yang punya kos & butuh penghuni 😏)")
with st.form("form_kos_lucu"):
    nama = st.text_input("📝 Nama Kos")
    lokasi_baru = st.text_input("🌍 Lokasi Kos")
    harga = st.number_input("💵 Harga (Rp)", min_value=0)
    fasilitas = st.text_input("🛠️ Fasilitas (pisahkan dengan ';')")
    submit = st.form_submit_button("✨ Tambahkan Kos!")

    if submit:
        if not nama or not lokasi_baru or harga <= 0:
            st.error("🥲 Lengkapi semua data ya, jangan kasih harapan palsu!")
        else:
            new_data = pd.DataFrame({
                'nama': [nama],
                'lokasi': [lokasi_baru],
                'harga': [harga],
                'fasilitas': [fasilitas]
            })
            new_data.to_csv("kos_data.csv", mode='a', header=False, index=False)
            st.success("🎉 Kos baru berhasil ditambahkan! Refresh dulu, biar muncul 😉")

# 🚀 Penutup
st.markdown("---")
st.info("📢 *Tips hemat: pilih kos dekat kampus biar gak boros pulsa & bensin!* 😜")
st.caption("Dibuat dengan ❤️ oleh ChatGPT dan kamu.")
