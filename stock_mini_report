import streamlit as st
import requests

st.title("📊 Stock Market MINI Analyst Report")

# STEP 1: INPUT
symbol = st.text_input("Enter NSE Stock Symbol (example: RELIANCE, TCS, INFY)").upper()

if st.button("Generate Report"):

    if symbol:

        try:
            # STEP 2: CREATE SESSION
            session = requests.Session()
            headers = {
                "User-Agent": "Mozilla/5.0",
                "Accept-Language": "en-US,en;q=0.9"
            }

            session.get("https://www.nseindia.com", headers=headers)

            # STEP 3: FETCH DATA
            url = f"https://www.nseindia.com/api/quote-equity?symbol={symbol}"
            response = session.get(url, headers=headers)

            if response.status_code != 200:
                st.error("Failed to fetch data. Please check the symbol or try again later.")
            else:
                data = response.json()

                if "info" not in data:
                    st.error("Invalid stock symbol or data not available.")
                else:

                    # STEP 4: EXTRACT DATA
                    name = data.get("info", {}).get("companyName", "N/A")
                    last_price = data.get("priceInfo", {}).get("lastPrice", None)
                    change_percent = data.get("priceInfo", {}).get("pChange", None)
                    day_high = data.get("priceInfo", {}).get("intraDayHighLow", {}).get("max", None)
                    day_low = data.get("priceInfo", {}).get("intraDayHighLow", {}).get("min", None)
                    week52_high = data.get("priceInfo", {}).get("weekHighLow", {}).get("max", None)
                    week52_low = data.get("priceInfo", {}).get("weekHighLow", {}).get("min", None)
                    volume = data.get("securityWiseDP", {}).get("quantityTraded", "Not Available")

                    # STEP 5: GENERATE INSIGHTS (UNCHANGED)
                    insights = []

                    if change_percent is not None:
                        if change_percent > 1:
                            insights.append("Strong bullish sentiment today.")
                        elif change_percent > 0:
                            insights.append("Mild positive movement observed.")
                        elif change_percent < -1:
                            insights.append("Strong bearish sentiment today.")
                        else:
                            insights.append("Market movement is relatively flat.")

                    if last_price and day_high and day_low:
                        midpoint = (day_high + day_low) / 2
                        if last_price > midpoint:
                            insights.append("Stock trading in upper half of intraday range (intraday strength).")
                        else:
                            insights.append("Stock trading in lower half of intraday range (intraday weakness).")

                    if last_price and week52_high and week52_low:
                        position_52 = ((last_price - week52_low) / (week52_high - week52_low)) * 100
                        if position_52 > 80:
                            insights.append("Stock is near its 52-week high (momentum zone).")
                        elif position_52 < 30:
                            insights.append("Stock is closer to its 52-week low (value zone).")
                        else:
                            insights.append("Stock is trading in mid-range of its yearly movement.")

                    if volume == "Not Available":
                        insights.append("Volume data not available for analysis.")
                    else:
                        insights.append("Volume data available for trading activity analysis.")

                    # STEP 6: DISPLAY OUTPUT (Converted from print → Streamlit)

                    st.subheader("📌 MINI ANALYST REPORT")

                    col1, col2 = st.columns(2)

                    col1.metric("Current Price", last_price)
                    col2.metric("Change %", round(change_percent, 2) if change_percent else "N/A")

                    st.write(f"**Company:** {name}")
                    st.write(f"**Day Range:** {day_low} - {day_high}")
                    st.write(f"**52 Week Range:** {week52_low} - {week52_high}")
                    st.write(f"**Volume:** {volume}")

                    st.write("### 🔍 Key Insights")
                    for i, insight in enumerate(insights, 1):
                        st.write(f"{i}. {insight}")

                    st.success("✅ Report Generated Successfully")

        except:
            st.error("Something went wrong. Please try again.")

    else:
        st.warning("Please enter a stock symbol.")
