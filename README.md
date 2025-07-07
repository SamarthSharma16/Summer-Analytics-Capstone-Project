\section*{Dynamic Pricing for Urban Parking Lots: Project Report}

\subsection*{Introduction}
This project addresses the challenge of optimizing urban parking lot utilization through \textbf{dynamic, data-driven pricing}. By leveraging real-time data and advanced modeling, the goal is to ensure efficient use of parking spaces, reduce congestion, and provide fair pricing for users. The solution is implemented in Python using Pandas, Numpy, Pathway for real-time simulation, and Bokeh for interactive visualization.

\subsection*{Problem Statement}
Urban parking spaces face fluctuating demand throughout the day. Static pricing often leads to either overcrowding or underutilization. The objective was to:
\begin{itemize}
    \item Develop a dynamic pricing engine for 14 parking spaces.
    \item Update prices in real time based on:
        \begin{itemize}
            \item Occupancy history
            \item Queue length
            \item Traffic level
            \item Special events
            \item Vehicle type
            \item Competitor prices
        \end{itemize}
    \item Ensure price changes are smooth, explainable, and bounded.
    \item Visualize results in real time.
\end{itemize}

\subsection*{Dataset Overview}
Each record in the dataset contains:
\begin{center}
\begin{tabular}{|l|l|}
\hline
\textbf{Feature} & \textbf{Description} \\
\hline
ID & Unique row index \\
SystemCodeNumber & Parking lot identifier \\
Capacity & Maximum vehicles allowed \\
Latitude, Longitude & Lot location \\
Occupancy & Current parked vehicles \\
VehicleType & Type of incoming vehicle (car, bike, truck) \\
TrafficConditionNearby & Traffic level (low, average, high) \\
QueueLength & Vehicles waiting \\
IsSpecialDay & 1 if special day/event, else 0 \\
LastUpdatedDate/Time & Timestamp \\
\hline
\end{tabular}
\end{center}

\subsection*{Implementation Summary}

\subsubsection*{1. Data Ingestion and Preprocessing}
\begin{itemize}
    \item Loaded the dataset into a Pandas DataFrame.
    \item Ensured all columns matched expected types and names.
    \item Used Pathway for simulating real-time data ingestion.
\end{itemize}

\subsubsection*{2. Feature Engineering}
\begin{itemize}
    \item Converted categorical variables (traffic, vehicle type) into numerical values for modeling.
    \item Created helper functions for feature mapping.
\end{itemize}

\subsubsection*{3. Pricing Models}

\textbf{Model 1: Baseline Linear Model}
\begin{itemize}
    \item \textbf{Logic:} Price increases linearly with occupancy rate.
    \item \textbf{Formula:} 
    \[
    \text{Price}_{t+1} = \text{BasePrice} + \alpha \cdot \left(\frac{\text{Occupancy}}{\text{Capacity}}\right)
    \]
    \item \textbf{Purpose:} Establishes a simple, explainable baseline.
\end{itemize}

\textbf{Model 2: Demand-Based Model}
\begin{itemize}
    \item \textbf{Logic:} Incorporates occupancy, queue length, traffic, special days, and vehicle type into a demand function.
    \item \textbf{Formula:}
    \[
    \text{Demand} = \alpha \cdot \left(\frac{\text{Occupancy}}{\text{Capacity}}\right) + \beta \cdot \text{QueueLength} - \gamma \cdot \text{Traffic} + \delta \cdot \text{IsSpecialDay} + \epsilon \cdot \text{VehicleTypeWeight}
    \]
    \[
    \text{Price}_t = \text{BasePrice} \cdot (1 + \lambda \cdot \text{NormalizedDemand})
    \]
    \item \textbf{Features:} Demand is normalized; price is bounded (not less than 0.5$\times$ and not more than 2$\times$ base).
\end{itemize}

\textbf{Model 3: Competitive Pricing Model}
\begin{itemize}
    \item \textbf{Logic:} Adjusts price based on competitor lots' prices and proximity.
    \item \textbf{Rules:}
    \begin{itemize}
        \item If the lot is full and competitors are cheaper, suggest rerouting or lower price.
        \item If nearby lots are expensive, allow price to increase.
    \end{itemize}
    \item \textbf{Implementation:} Used Euclidean distance for proximity and compared demand-based prices across lots.
\end{itemize}

\subsubsection*{4. Batch Processing and Output}
\begin{itemize}
    \item Computed all three prices for each record in the dataset.
    \item Saved all results to a CSV file for further analysis.
\end{itemize}

\textbf{Example Output Table:}
\begin{center}
\begin{tabular}{|c|c|c|c|c|c|}
\hline
ID & SystemCodeNumber & Timestamp & Price\_Linear & Price\_Demand & Price\_Competitive \\
\hline
0 & BHMBCCMKT01 & 04-10-2016 07:59:00 & 10.53 & 12.18 & 12.18 \\
1 & BHMBCCMKT01 & 04-10-2016 08:25:00 & 10.55 & 12.24 & 12.24 \\
2 & BHMBCCMKT01 & 04-10-2016 08:59:00 & 10.69 & 12.56 & 12.56 \\
3 & BHMBCCMKT01 & 04-10-2016 09:32:00 & 10.93 & 13.09 & 13.09 \\
4 & BHMBCCMKT01 & 04-10-2016 09:59:00 & 11.30 & 13.87 & 13.87 \\
\hline
\end{tabular}
\end{center}

\subsection*{Visualization}
\begin{itemize}
    \item Used Bokeh to create interactive line plots of price evolution over time for each parking lot.
    \item Plots compare all three pricing models, providing visual justification for price changes.
    \item Addressed Bokeh-specific issues (e.g., duplicate x-axis labels) by deduplicating or aggregating timestamps.
    \item Additional visualizations included occupancy and queue length over time, price distributions, heatmaps of occupancy, and geographic maps of lots colored by occupancy or price.
\end{itemize}

\subsection*{Key Challenges and Solutions}
\begin{itemize}
    \item \textbf{Slow Processing:} The competitive pricing model's nested looping was slow for large datasets. Optimization suggestions included vectorization and precomputing competitor groups.
    \item \textbf{Pathway Integration:} Used CSV reading for Pathway ingestion, as direct DataFrame conversion was unsupported.
    \item \textbf{Plotting Errors:} Resolved Bokeh's duplicate factor errors by ensuring unique timestamps for categorical axes.
\end{itemize}

\subsection*{Deliverables}
\begin{itemize}
    \item Well-commented, modular code in notebook cells.
    \item CSV file containing all computed prices.
    \item Interactive visualizations for real-time price trends.
    \item Comprehensive report documenting logic, assumptions, and results.
\end{itemize}

\subsection*{Conclusion}
This project successfully demonstrates a scalable, modular approach to dynamic pricing for urban parking. The models developed are transparent, tunable, and ready for further extension or deployment in real-world scenarios. The workflow, from data ingestion to advanced modeling and visualization, provides a robust foundation for intelligent parking management systems.
