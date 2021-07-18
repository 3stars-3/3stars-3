import pandas as pd                       #to perform data manipulation and analysis
	import numpy as np                        #to cleanse data
	from datetime import datetime             #to manipulate dates
	import plotly.express as px               #to create interactive charts
	import plotly.graph_objects as go         #to create interactive charts
	from jupyter_dash import JupyterDash      #to build Dash apps from Jupyter environments
	import dash_core_components as dcc        #to get components for interactive user interfaces
	import dash_html_components as html       
df = pd.read_csv("transactions.csv") 
	df[6:10]
df = df[df.category != "Internal"]
df['category'] = np.where(df['description'].str
df['starts'] = list( 
	    map(lambda x: x.startswith('To '), df['description'])) 
	df.loc[df.starts == True, 'category'] = "Transfer"
	df.drop('starts', axis=1, inplace=True)
df.category.replace(["Coffee", "Eating out", "Takeaway", "Lunch"], "Food",inplace=True)
Net_Worth_Table = df.groupby('year_month')['amount'].sum().reset_index(name ='sum')
	Net_Worth_Table['cumulative sum'] = Net_Worth_Table['sum'].cumsum()
	Net_Worth_Chart = go.Figure(
	    data = go.Scatter(x = Net_Worth_Table["year_month"], y = Net_Worth_Table["cumulative sum"]),
	    layout = go.Layout(
	        title = go.layout.Title(text = "Net Worth Over Time")
	    )
	)
	Net_Worth_Chart.update_layout(
	    xaxis_title = "Date",
	    yaxis_title = "Net Worth (£)",
	    hovermode = 'x unified'
	    )
	Net_Worth_Chart.update_xaxes(
	    tickangle = 45)
	Net_Worth_Chart.show()
df = df[df.category != "Income"] 
	df.amount = df.amount*(-1) 
	Total_Monthly_Expenses_Table = df.groupby('year_month')['amount'].sum().reset_index(name = 'sum')
	Total_Monthly_Expenses_Chart = px.bar(Total_Monthly_Expenses_Table, x = "year_month", y = "sum", title = "Total Monthly Expenses")
	Total_Monthly_Expenses_Chart.update_yaxes(title = 'Expenses (£)', visible = True, showticklabels = True)
	Total_Monthly_Expenses_Chart.update_xaxes(title = 'Date', visible = True, showticklabels = True)
	Total_Monthly_Expenses_Chart.show()
Expenses_Breakdown_Table = pd.pivot_table(df, values = ['amount'], index = ['category', 'year_month'], aggfunc=sum).reset_index()
	Expenses_Breakdown_Table.columns = [x.upper() for x in Expenses_Breakdown_Table.columns]
	Expenses_Breakdown_Chart = px.line(Expenses_Breakdown_Table, x='year_month', y="amount", title="Expenses Breakdown", color = 'category')
	Expenses_Breakdown_Chart.update_yaxes(title='Expenses (£)', visible=True, showticklabels=True)
	Expenses_Breakdown_Chart.update_xaxes(title='Date', visible=True, showticklabels=True)
	Expenses_Breakdown_Chart.show()

# Build App
	app = JupyterDash(__name__)
	

	app.layout = html.Div([   
	    html.Div([
	        html.H1(str(latest_date)+" Personal Finance Summary",style={'text-align':'center'}),
	        dcc.Graph(figure = Net_Worth_Chart)
	    ]),  
	    html.Div([
	        dcc.Graph(figure = Total_Monthly_Expenses_Chart)
	    ]),   
	    html.Div([
	        dcc.Graph(figure = Expenses_Breakdown_Chart)
	    ])
	])
	    
	# Run app and display result
	app.run_server(mode='external')
