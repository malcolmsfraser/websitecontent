---
title: "Streamlit Demo"
author: Malcolm Smith Fraser
date: 2021-10-15T20:14:13Z
tags:
series:
draft: true
---

In this blog post I will walk through how I used streamlit to build a dashboard 
for visualizing and comparing data on post-PhD activities of 2017 graduates by sex.

Streamlit is one of most popular python webapp and dashboarding tools out there today.
It links connects almost seamlessly with many of the visualization tools in the 
Python DS ecosystem, and has tons of options for customization.

![Example image](/images/streamlit-demo-fulldashboard.png)

Let's get started!

## **Part 1: Analyze the data**
Before moving to Streamlit, I started in a Jupyter notebook to explore the data, 
find some interesting visualizations and overall just get a feel for the data - 
both content and structure. 

The data I used came from the National Center for Science and Engineering Statistics 
(https://ncses.nsf.gov/pubs/nsf19301/data), and uses the table that covers
postgraduation plans of doctorate recipients, by sex and broad field of study for 2017.
The is essentially 3 tables stacked together: one for male, one for female, and 
a combined sex table. Also for this data exploration I was primarily interested 
in exploring enginnering, math and science degree recipients.

Loading, splitting and cleaning the data
```{python}
# Function for loading and splitting data
def load_data():
    data=pd.read_excel("sed17-sr-tab055.xlsx",header=3,index_col=0)
    female_data = data[-42:]
    male_data = data[42:-42]
    combined_data = data[:42]
    return male_data, female_data, combined_data
  
# Function for cleaning data
def clean_filter(df):
    sci_eng = (
        df.
        filter(regex='(science|Engineer)').
        rename({"Life sciencesa":"Life sciences"}, axis=1).
        dropna(axis=0).
        T.
        reset_index().
        rename_axis(None, axis=1).
        rename({"index":"Field",
                "All doctorate recipients (number)c": "Total doctorate recipients",
                "Male doctorate recipients (number)": "Total doctorate recipients",
                "Female doctorate recipients (number)": "Total doctorate recipients"},axis=1)
    )
    return sci_eng
# Load data
male_data, female_data, full_data = load_data()

# Clean data
male_data_clean = clean_filter(male_data)
female_data_clean = clean_filter(female_data)
full_data_clean = clean_filter(full_data)

# Organize data splits
data_split = {
    "male": male_data_clean,
    "female": female_data_clean,
    "combined": combined_data_clean
}
```

Now that the data is split reformatted it looks like this

![Example image](/images/streamlit-demo-dataframe1.png)

Next I tried to understand how I could visualize the data. To keep things simple,
I looked at some barplots I created with plotly express.
```{python}
graph = px.bar(
    data_split['combined'].sort_values("Total doctorate recipients", ascending=False), 
    x = 'Field',
    y = "Total doctorate recipients",
    labels = {'Number of cases':'Number of cases in %s'},
    color = 'Field'
    )
```

{{< rawhtml >}}
        <div id="f49aa1f2-7772-4758-b8bc-ed3fc9f18765" class="plotly-graph-div" style="height:100%; width:100%;"></div>
            <script type="text/javascript">
                
                    window.PLOTLYENV=window.PLOTLYENV || {};
                    
                if (document.getElementById("f49aa1f2-7772-4758-b8bc-ed3fc9f18765")) {
                    Plotly.newPlot(
                        'f49aa1f2-7772-4758-b8bc-ed3fc9f18765',
                        [{"alignmentgroup": "True", "hovertemplate": "Field=%{x}<br>Total doctorate recipients=%{y}<extra></extra>", "legendgroup": "Life sciences", "marker": {"color": "#636efa"}, "name": "Life sciences", "offsetgroup": "Life sciences", "orientation": "v", "showlegend": true, "textposition": "auto", "type": "bar", "x": ["Life sciences"], "xaxis": "x", "y": [12592.0], "yaxis": "y"}, {"alignmentgroup": "True", "hovertemplate": "Field=%{x}<br>Total doctorate recipients=%{y}<extra></extra>", "legendgroup": "Engineering", "marker": {"color": "#EF553B"}, "name": "Engineering", "offsetgroup": "Engineering", "orientation": "v", "showlegend": true, "textposition": "auto", "type": "bar", "x": ["Engineering"], "xaxis": "x", "y": [9843], "yaxis": "y"}, {"alignmentgroup": "True", "hovertemplate": "Field=%{x}<br>Total doctorate recipients=%{y}<extra></extra>", "legendgroup": "Psychology and social sciences", "marker": {"color": "#00cc96"}, "name": "Psychology and social sciences", "offsetgroup": "Psychology and social sciences", "orientation": "v", "showlegend": true, "textposition": "auto", "type": "bar", "x": ["Psychology and social sciences"], "xaxis": "x", "y": [9079], "yaxis": "y"}, {"alignmentgroup": "True", "hovertemplate": "Field=%{x}<br>Total doctorate recipients=%{y}<extra></extra>", "legendgroup": "Physical sciences and earth sciences", "marker": {"color": "#ab63fa"}, "name": "Physical sciences and earth sciences", "offsetgroup": "Physical sciences and earth sciences", "orientation": "v", "showlegend": true, "textposition": "auto", "type": "bar", "x": ["Physical sciences and earth sciences"], "xaxis": "x", "y": [6081.0], "yaxis": "y"}, {"alignmentgroup": "True", "hovertemplate": "Field=%{x}<br>Total doctorate recipients=%{y}<extra></extra>", "legendgroup": "Mathematics and computer sciences", "marker": {"color": "#FFA15A"}, "name": "Mathematics and computer sciences", "offsetgroup": "Mathematics and computer sciences", "orientation": "v", "showlegend": true, "textposition": "auto", "type": "bar", "x": ["Mathematics and computer sciences"], "xaxis": "x", "y": [3843], "yaxis": "y"}],
                        {"barmode": "relative", "legend": {"title": {"text": "Field"}, "tracegroupgap": 0}, "margin": {"t": 60}, "template": {"data": {"bar": [{"error_x": {"color": "#2a3f5f"}, "error_y": {"color": "#2a3f5f"}, "marker": {"line": {"color": "#E5ECF6", "width": 0.5}}, "type": "bar"}], "barpolar": [{"marker": {"line": {"color": "#E5ECF6", "width": 0.5}}, "type": "barpolar"}], "carpet": [{"aaxis": {"endlinecolor": "#2a3f5f", "gridcolor": "white", "linecolor": "white", "minorgridcolor": "white", "startlinecolor": "#2a3f5f"}, "baxis": {"endlinecolor": "#2a3f5f", "gridcolor": "white", "linecolor": "white", "minorgridcolor": "white", "startlinecolor": "#2a3f5f"}, "type": "carpet"}], "choropleth": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "type": "choropleth"}], "contour": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "colorscale": [[0.0, "#0d0887"], [0.1111111111111111, "#46039f"], [0.2222222222222222, "#7201a8"], [0.3333333333333333, "#9c179e"], [0.4444444444444444, "#bd3786"], [0.5555555555555556, "#d8576b"], [0.6666666666666666, "#ed7953"], [0.7777777777777778, "#fb9f3a"], [0.8888888888888888, "#fdca26"], [1.0, "#f0f921"]], "type": "contour"}], "contourcarpet": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "type": "contourcarpet"}], "heatmap": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "colorscale": [[0.0, "#0d0887"], [0.1111111111111111, "#46039f"], [0.2222222222222222, "#7201a8"], [0.3333333333333333, "#9c179e"], [0.4444444444444444, "#bd3786"], [0.5555555555555556, "#d8576b"], [0.6666666666666666, "#ed7953"], [0.7777777777777778, "#fb9f3a"], [0.8888888888888888, "#fdca26"], [1.0, "#f0f921"]], "type": "heatmap"}], "heatmapgl": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "colorscale": [[0.0, "#0d0887"], [0.1111111111111111, "#46039f"], [0.2222222222222222, "#7201a8"], [0.3333333333333333, "#9c179e"], [0.4444444444444444, "#bd3786"], [0.5555555555555556, "#d8576b"], [0.6666666666666666, "#ed7953"], [0.7777777777777778, "#fb9f3a"], [0.8888888888888888, "#fdca26"], [1.0, "#f0f921"]], "type": "heatmapgl"}], "histogram": [{"marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "histogram"}], "histogram2d": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "colorscale": [[0.0, "#0d0887"], [0.1111111111111111, "#46039f"], [0.2222222222222222, "#7201a8"], [0.3333333333333333, "#9c179e"], [0.4444444444444444, "#bd3786"], [0.5555555555555556, "#d8576b"], [0.6666666666666666, "#ed7953"], [0.7777777777777778, "#fb9f3a"], [0.8888888888888888, "#fdca26"], [1.0, "#f0f921"]], "type": "histogram2d"}], "histogram2dcontour": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "colorscale": [[0.0, "#0d0887"], [0.1111111111111111, "#46039f"], [0.2222222222222222, "#7201a8"], [0.3333333333333333, "#9c179e"], [0.4444444444444444, "#bd3786"], [0.5555555555555556, "#d8576b"], [0.6666666666666666, "#ed7953"], [0.7777777777777778, "#fb9f3a"], [0.8888888888888888, "#fdca26"], [1.0, "#f0f921"]], "type": "histogram2dcontour"}], "mesh3d": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "type": "mesh3d"}], "parcoords": [{"line": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "parcoords"}], "pie": [{"automargin": true, "type": "pie"}], "scatter": [{"marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "scatter"}], "scatter3d": [{"line": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "scatter3d"}], "scattercarpet": [{"marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "scattercarpet"}], "scattergeo": [{"marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "scattergeo"}], "scattergl": [{"marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "scattergl"}], "scattermapbox": [{"marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "scattermapbox"}], "scatterpolar": [{"marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "scatterpolar"}], "scatterpolargl": [{"marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "scatterpolargl"}], "scatterternary": [{"marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "scatterternary"}], "surface": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "colorscale": [[0.0, "#0d0887"], [0.1111111111111111, "#46039f"], [0.2222222222222222, "#7201a8"], [0.3333333333333333, "#9c179e"], [0.4444444444444444, "#bd3786"], [0.5555555555555556, "#d8576b"], [0.6666666666666666, "#ed7953"], [0.7777777777777778, "#fb9f3a"], [0.8888888888888888, "#fdca26"], [1.0, "#f0f921"]], "type": "surface"}], "table": [{"cells": {"fill": {"color": "#EBF0F8"}, "line": {"color": "white"}}, "header": {"fill": {"color": "#C8D4E3"}, "line": {"color": "white"}}, "type": "table"}]}, "layout": {"annotationdefaults": {"arrowcolor": "#2a3f5f", "arrowhead": 0, "arrowwidth": 1}, "coloraxis": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "colorscale": {"diverging": [[0, "#8e0152"], [0.1, "#c51b7d"], [0.2, "#de77ae"], [0.3, "#f1b6da"], [0.4, "#fde0ef"], [0.5, "#f7f7f7"], [0.6, "#e6f5d0"], [0.7, "#b8e186"], [0.8, "#7fbc41"], [0.9, "#4d9221"], [1, "#276419"]], "sequential": [[0.0, "#0d0887"], [0.1111111111111111, "#46039f"], [0.2222222222222222, "#7201a8"], [0.3333333333333333, "#9c179e"], [0.4444444444444444, "#bd3786"], [0.5555555555555556, "#d8576b"], [0.6666666666666666, "#ed7953"], [0.7777777777777778, "#fb9f3a"], [0.8888888888888888, "#fdca26"], [1.0, "#f0f921"]], "sequentialminus": [[0.0, "#0d0887"], [0.1111111111111111, "#46039f"], [0.2222222222222222, "#7201a8"], [0.3333333333333333, "#9c179e"], [0.4444444444444444, "#bd3786"], [0.5555555555555556, "#d8576b"], [0.6666666666666666, "#ed7953"], [0.7777777777777778, "#fb9f3a"], [0.8888888888888888, "#fdca26"], [1.0, "#f0f921"]]}, "colorway": ["#636efa", "#EF553B", "#00cc96", "#ab63fa", "#FFA15A", "#19d3f3", "#FF6692", "#B6E880", "#FF97FF", "#FECB52"], "font": {"color": "#2a3f5f"}, "geo": {"bgcolor": "white", "lakecolor": "white", "landcolor": "#E5ECF6", "showlakes": true, "showland": true, "subunitcolor": "white"}, "hoverlabel": {"align": "left"}, "hovermode": "closest", "mapbox": {"style": "light"}, "paper_bgcolor": "white", "plot_bgcolor": "#E5ECF6", "polar": {"angularaxis": {"gridcolor": "white", "linecolor": "white", "ticks": ""}, "bgcolor": "#E5ECF6", "radialaxis": {"gridcolor": "white", "linecolor": "white", "ticks": ""}}, "scene": {"xaxis": {"backgroundcolor": "#E5ECF6", "gridcolor": "white", "gridwidth": 2, "linecolor": "white", "showbackground": true, "ticks": "", "zerolinecolor": "white"}, "yaxis": {"backgroundcolor": "#E5ECF6", "gridcolor": "white", "gridwidth": 2, "linecolor": "white", "showbackground": true, "ticks": "", "zerolinecolor": "white"}, "zaxis": {"backgroundcolor": "#E5ECF6", "gridcolor": "white", "gridwidth": 2, "linecolor": "white", "showbackground": true, "ticks": "", "zerolinecolor": "white"}}, "shapedefaults": {"line": {"color": "#2a3f5f"}}, "ternary": {"aaxis": {"gridcolor": "white", "linecolor": "white", "ticks": ""}, "baxis": {"gridcolor": "white", "linecolor": "white", "ticks": ""}, "bgcolor": "#E5ECF6", "caxis": {"gridcolor": "white", "linecolor": "white", "ticks": ""}}, "title": {"x": 0.05}, "xaxis": {"automargin": true, "gridcolor": "white", "linecolor": "white", "ticks": "", "title": {"standoff": 15}, "zerolinecolor": "white", "zerolinewidth": 2}, "yaxis": {"automargin": true, "gridcolor": "white", "linecolor": "white", "ticks": "", "title": {"standoff": 15}, "zerolinecolor": "white", "zerolinewidth": 2}}}, "xaxis": {"anchor": "y", "categoryarray": ["Life sciences", "Engineering", "Psychology and social sciences", "Physical sciences and earth sciences", "Mathematics and computer sciences"], "categoryorder": "array", "domain": [0.0, 1.0], "title": {"text": "Field"}}, "yaxis": {"anchor": "x", "domain": [0.0, 1.0], "title": {"text": "Total doctorate recipients"}}},
                        {"responsive": true}
                    )
                };
                
            </script>
        </div>
{{< /rawhtml >}}

Now that I have my data formatted and have the plots I want, I can start building the dashboard.

## **Part 2: Build the dashboard**

The idea for the dashboard is to have a sidebar where the user can select the
gender of the data they want to visualize as well as the variable they want to 
visualize for that gender. I also want the user to be able decide if they want to
compare the anlysis and select which sex/combined they will compare with. 
Once selected, the visualizations will be displayed in the main area, with an
option to display the raw data. Lastly, want to give the user an option to hide 
the graph.


Before we start I want to organize the data in a way that makes it easy for the
user to pic the variables to visualize. From the structure of the table, I noticed 
that the columns could be grouped into the following categories:
- Post Grad Status (#),
- Post Grad Employment Type (%)
- Post Grad Study Type (%)
- Post Grad Location (%)
along with a column for total degree recipients and a columns for the degree field.

I decided to record the splits based on the categories. The decision to do this
will become more obvious when we start building the dashboard.
```{python}
total_phd_recipients = full_data_clean.iloc[:,0:1].columns.append(full_data_clean.iloc[:,1:2].columns)
post_grad_location = full_data_clean.iloc[:,0:1].columns.append(full_data_clean.iloc[:,-12:-1].columns)
post_grad_status = full_data_clean.iloc[:,0:1].columns.append(full_data_clean.iloc[:,2:6].columns)
post_grad_study_type = full_data_clean.iloc[:,0:1].columns.append(full_data_clean.iloc[:,6:8].columns)
post_grad_employment_type = full_data_clean.iloc[:,0:1].columns.append(full_data_clean.iloc[:,8:13].columns)
```

Alright, now that that's out of the way lets get building.

### *Building the sidebar*

Create a new python file named dashboard.py and import your packeges.
```{python}
import streamlit as st
import pandas as pd
import numpy as np
import plotly.express as px
```

Setup the streamlit dashboard config, title, and headers/subheaders.
```{python}
st.set_page_config(page_title="My Dashboard",layout='wide')
st.title("**Post-PhD activities for US Science and Engineering Grads**")
st.header("Gender Level Analysis")
st.caption("Data from https://ncses.nsf.gov/pubs/nsf19301/data")
```

Then load, filter, and organize your data the exact same way as we did when analyzing the data (see above).

In the sidebar I will use select boxes for selecting the gender and category.
For selecting the individual variables within the categories in will use a radio
button widget.

```{python}
# select gender
select_gender = st.sidebar.selectbox('Select Gender',["male","female","combined"],key=1)

# select category
select_category = st.sidebar.selectbox("Analysis Category", ["Total Recipients", "Post Grad Status (#)",'Post Grad Employment Type (%)',"Post Grad Study Type (%)","Post Grad Location (%)"])

# subset data based on gender and category
select_data = data_split[select_gender][categories[select_category]]

# select variable from category
select_variable = st.sidebar.radio("Variable", select_data.drop("Field",axis=1).columns)
```

Now if you run `streamlit run dashboard.py` you should get something like this.
![Example image](/images/streamlit-demo-sidebar1.png)

We will get to the "compare" options later.

### Dashboard Body

In the body of our dashboard we add the option to view the raw data.
```{python}
if st.checkbox('Show raw data'):
    st.subheader('Raw data')
    st.dataframe(select_data)
```

Next we will add the plots - that will only show if the option to hide the graphs is not selected.
We also want to plot in two columns in case we decide to make a comparison plot.
```{python}
# Create two columns
col1, col2 = st.columns(2)

# Create "Hide Graph" option
if not st.checkbox('Hide Graph', False, key=1):
    
    # Plot title
    col1.markdown(f"### {select_variable}: {select_gender}")
    
    # Graph data with the variables selected above
    graph = px.bar(
        select_data.sort_values(select_variable, ascending=False), 
        x='Field',
        y=select_variable,
        labels={'Number of cases':'Number of cases in %s'},
        color='Field')
        
    # Optional: Adjust layout
    graph.update_layout(width=700)
    graph.layout.update(showlegend=False)
    
    # Add the plot to the streamlit dashboard in column 1
    col1.plotly_chart(graph,width=700)
```

Since we only want to compare a visualization if we already have generated a baseline one,
now is when we add the comparison information. To do this we add the following 
inside the same "Hide Graph" if statement.
```{python}
    # Create a "Compare" checkbox in the sidebar
    if st.sidebar.checkbox("Compare",True, key=2):
        
        # Select the gender to compare with and grab the data split
        select2 = st.sidebar.selectbox('Select Gender',["female","male","combined"],key=2)
        select_data2 = data_split[select2]
        
        # Graph the comparison plot
        col2.markdown(f"### {select_variable}: {select2}")
        graph2 = px.bar(
            select_data2.sort_values(select_variable, ascending=False), 
            x='Field',
            y=select_variable,
            labels={'Number of cases':'Number of cases in %s'},
            color='Field')
        graph2.layout.update(showlegend=False)
        
         # Add the plot to the streamlit dashboard in column 1
        col2.plotly_chart(graph2)  
```

The resulting dashboard should look something like this
![Example image](/images/streamlit-demo-fulldashboard.png)


I hope you found this walkthrough useful! 


