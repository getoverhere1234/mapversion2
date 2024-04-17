import streamlit as st
import folium
import pandas as pd
from streamlit_folium import folium_static
from geopy.distance import geodesic

def add_click_events(map_object):
    """Add click events to the Folium map to display coordinates."""
    map_object.add_child(folium.ClickForMarker(popup="Coordinates: {lat}, {lon}"))

def check_distance(lat1, lon1, lat2, lon2):
    """Calculate the distance between two coordinates."""
    coord1 = (lat1, lon1)
    coord2 = (lat2, lon2)
    return geodesic(coord1, coord2).kilometers

def display_legend():
    """Display legend for the codes."""
    st.sidebar.header("Legend")
    st.sidebar.markdown("**Code 1**: Low pH, N, Zn and Mn")
    st.sidebar.markdown("**Code 2**: Low pH, Zn and Mn")
    st.sidebar.markdown("**Code 3**: Low pH, N and Mn")

def main():
    st.title("Soil Status Map")

    # File uploader widget
    uploaded_file = st.file_uploader("Choose a CSV file", type=["csv"])

    if uploaded_file is not None:
        # Read data from uploaded CSV file
        data = pd.read_csv(uploaded_file)

        # Check if the required columns are present
        if 'Latitute' in data.columns and 'Longititude' in data.columns and 'Code' in data.columns:
            # Display legend
            display_legend()

            # Create a base map
            m = folium.Map(location=[data['Latitute'].mean(), data['Longititude'].mean()], zoom_start=10)

            # Add click events to display coordinates
            add_click_events(m)

            # Add markers to the map based on the data
            for index, row in data.iterrows():
                folium.Marker(
                    location=[row['Latitute'], row['Longititude']],
                    popup=f"Coordinates: ({row['Latitute']}, {row['Longititude']})<br>Code: {row['Code']}",
                    icon=folium.Icon(color='red' if row['Code'] == 1 else 'blue' if row['Code'] == 2 else 'green')
                ).add_to(m)

            # Display the map using folium_static
            folium_static(m)

            # Add input boxes for manual coordinates
            st.sidebar.header("Check Distance to Coordinates")
            input_lat = st.sidebar.number_input("Enter Latitude:")
            input_lon = st.sidebar.number_input("Enter Longitude:")

            if st.sidebar.button("Check Distance"):
                # Check distance to each coordinate in the CSV file
                distances = []
                for _, row in data.iterrows():
                    distance = check_distance(input_lat, input_lon, row['Latitute'], row['Longititude'])
                    distances.append(distance)

                # Find the closest coordinate
                closest_index = distances.index(min(distances))
                closest_coord = (data.loc[closest_index, 'Latitute'], data.loc[closest_index, 'Longititude'])

                st.sidebar.write(f"Closest Coordinate: {closest_coord}")
                st.sidebar.write(f"Distance to Closest Coordinate: {min(distances):.2f} km")

                # Add marker for the manually entered coordinates
                folium.Marker(
                    location=[input_lat, input_lon],
                    popup=f"Entered Coordinates: ({input_lat}, {input_lon})",
                    icon=folium.Icon(color='purple')
                ).add_to(m)

            # Display the updated map using folium_static
            folium_static(m)

        else:
            st.warning("The uploaded CSV file does not contain the required columns: 'Latitute', 'Longititude', 'Code'.")

if __name__ == "__main__":
    main()
