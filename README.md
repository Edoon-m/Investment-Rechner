# Investment-Rechner
import streamlit as st
import matplotlib.pyplot as plt

st.set_page_config(page_title="Investmentrechner", layout="centered")

st.title("Investment-Rechner (Halal-kompatibel)")

# Eingabefelder
startkapital = st.number_input("Startkapital (€)", min_value=0, value=10000)
monatliche_einzahlung = st.number_input("Monatliche Einzahlung (€)", min_value=0, value=1000)
rendite = st.number_input("Rendite (% p.a.)", min_value=0.0, max_value=100.0, value=7.0)
laufzeit = st.slider("Anlagedauer (Jahre)", min_value=1, max_value=40, value=15)

# Berechnung
jahre = list(range(1, laufzeit + 1))
investiertes_kapital = []
endkapital_liste = []
gewinne = []
gewinne_nach_steuern = []

for jahr in jahre:
    monate = jahr * 12
    r = rendite / 100 / 12

    endkapital = startkapital * (1 + r) ** monate + monatliche_einzahlung * (((1 + r) ** monate - 1) / r)
    investition = startkapital + monatliche_einzahlung * monate
    gewinn = endkapital - investition

    steuerfreibetrag = min(1200 * jahr, gewinn)
    zu_versteuernder_gewinn = max(0, gewinn - steuerfreibetrag)
    steuer = zu_versteuernder_gewinn * 0.26375
    gewinn_nach_steuer = gewinn - steuer

    investiertes_kapital.append(investition)
    endkapital_liste.append(endkapital)
    gewinne.append(gewinn)
    gewinne_nach_steuern.append(gewinn_nach_steuer)

# Matplotlib Plot
fig, ax = plt.subplots(figsize=(8, 5))
ax.plot(jahre, investiertes_kapital, label="Investiertes Kapital", linestyle="--")
ax.plot(jahre, endkapital_liste, label="Endkapital")
ax.plot(jahre, gewinne, label="Gewinn (brutto)")
ax.plot(jahre, gewinne_nach_steuern, label="Gewinn nach Steuern", linestyle="-.")
ax.set_xlabel("Jahre")
ax.set_ylabel("Geldbetrag (€)")
ax.set_title("Vermögensentwicklung")
ax.grid(True)
ax.legend()

# Anzeigen
st.pyplot(fig)

# Letzter Wert als Zusammenfassung
st.subheader("Zusammenfassung nach {} Jahren".format(laufzeit))
st.write(f"**Investiert:** {investiertes_kapital[-1]:,.2f} €")
st.write(f"**Endkapital:** {endkapital_liste[-1]:,.2f} €")
st.write(f"**Gewinn:** {gewinne[-1]:,.2f} €")
st.write(f"**Gewinn nach Steuern:** {gewinne_nach_steuern[-1]:,.2f} €")
