# tinhlaikepdon
import streamlit as st

# =========================
# CẤU HÌNH TRANG
# =========================
st.set_page_config(
    page_title="App Gửi Tiết Kiệm",
    page_icon="💰",
    layout="centered"
)

# =========================
# TIÊU ĐỀ
# =========================
st.title("💰 Ứng dụng tính lãi tiền gửi tiết kiệm")
st.write("Nhập thông tin khoản tiền gửi để tính tiền lãi và tổng số tiền nhận được.")

# =========================
# NHẬP THÔNG TIN
# =========================

# Số tiền gửi
tien_gui = st.number_input(
    "💵 Số tiền gửi (VNĐ)",
    min_value=0.0,
    value=500_000_000.0,
    step=1_000_000.0,
    format="%.0f"
)

# Kỳ hạn
ky_han = st.number_input(
    "📅 Kỳ hạn (tháng)",
    min_value=1,
    value=3,
    step=1
)

# Lãi suất
lai_suat = st.number_input(
    "📈 Lãi suất (%/tháng)",
    min_value=0.0,
    value=1.0,
    step=0.1,
    format="%.2f"
)

# Hình thức nhận lãi
hinh_thuc = st.selectbox(
    "💳 Hình thức nhận lãi",
    [
        "Cuối kỳ",
        "Hàng tháng",
        "Hàng quý"
    ]
)

# Chuyển lãi suất từ % sang số thập phân
r = lai_suat / 100

# =========================
# TÍNH TOÁN
# =========================

if st.button("🧮 Tính lãi", use_container_width=True):

    if tien_gui <= 0:
        st.error("Vui lòng nhập số tiền gửi lớn hơn 0.")
    elif ky_han <= 0:
        st.error("Kỳ hạn phải lớn hơn 0.")
    elif lai_suat < 0:
        st.error("Lãi suất không được âm.")

    else:

        # --------------------------------
        # 1. NHẬN LÃI CUỐI KỲ
        # --------------------------------
        if hinh_thuc == "Cuối kỳ":

            # Lãi đơn
            tien_lai_dinh_ky = tien_gui * r * ky_han
            tong_lai = tien_lai_dinh_ky
            tong_tien = tien_gui + tong_lai

            mo_ta = (
                f"Bạn nhận toàn bộ tiền lãi sau {ky_han} tháng."
            )

        # --------------------------------
        # 2. NHẬN LÃI HÀNG THÁNG
        # --------------------------------
        elif hinh_thuc == "Hàng tháng":

            tien_lai_dinh_ky = tien_gui * r
            tong_lai = tien_lai_dinh_ky * ky_han
            tong_tien = tien_gui + tong_lai

            mo_ta = (
                "Tiền lãi được nhận mỗi tháng, "
                "không nhập lãi vào tiền gốc."
            )

        # --------------------------------
        # 3. NHẬN LÃI HÀNG QUÝ
        # --------------------------------
        else:

            # Số quý
            so_quy = ky_han // 3
            thang_le = ky_han % 3

            # Lãi mỗi quý
            lai_moi_quy = tien_gui * r * 3

            # Lãi của các quý
            tong_lai_quy = lai_moi_quy * so_quy

            # Lãi phần tháng lẻ
            lai_thang_le = tien_gui * r * thang_le

            tong_lai = tong_lai_quy + lai_thang_le
            tong_tien = tien_gui + tong_lai

            # Lãi định kỳ
            if ky_han >= 3:
                tien_lai_dinh_ky = lai_moi_quy
            else:
                tien_lai_dinh_ky = tien_gui * r * ky_han

            mo_ta = (
                "Tiền lãi được nhận mỗi quý. "
                "Nếu kỳ hạn không chia hết cho 3 tháng, "
                "phần tháng còn lại được tính theo số tháng thực tế."
            )

        # =========================
        # HIỂN THỊ KẾT QUẢ
        # =========================

        st.success("✅ Tính toán thành công!")

        st.subheader("📊 Kết quả")

        col1, col2 = st.columns(2)

        with col1:
            st.metric(
                "💵 Tiền lãi định kỳ",
                f"{tien_lai_dinh_ky:,.0f} VNĐ"
            )

        with col2:
            st.metric(
                "📈 Tổng tiền lãi",
                f"{tong_lai:,.0f} VNĐ"
            )

        st.metric(
            "💰 Tổng tiền gốc + lãi",
            f"{tong_tien:,.0f} VNĐ"
        )

        st.info(mo_ta)

        # =========================
        # CHI TIẾT
        # =========================

        with st.expander("🔎 Xem chi tiết khoản tiền gửi"):

            st.write(f"**Số tiền gửi:** {tien_gui:,.0f} VNĐ")
            st.write(f"**Kỳ hạn:** {ky_han} tháng")
            st.write(f"**Lãi suất:** {lai_suat:.2f}%/tháng")
            st.write(f"**Hình thức nhận lãi:** {hinh_thuc}")
            st.write(f"**Tổng tiền lãi:** {tong_lai:,.0f} VNĐ")
            st.write(f"**Tổng tiền nhận được:** {tong_tien:,.0f} VNĐ")
