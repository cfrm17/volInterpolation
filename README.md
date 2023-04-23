# Volatility Smile Interpolation Approach


Options priced by the Black-Scholes formula are quoted on the market by implied volatility. In other words, to price an interest rate European option, one needs to know forward rate, the strike level and volatility along with the discount rate. However, the market trading history has shown that it is not perfect to trade options solely based on the Black-Scholes model, simply because there are many other factors that falls out of the model assumptions. 

Let us take swaptions as an example. By assuming that the underlying forward swap rate follows a lognormal process, we can correctly price a swaption with the Black-Scholes formula if, for the given strike, a suitable volatility is used. It turns out that if the strike changes, the formula would not give the market price with the same volatility. 

The market practice to this issue (also named as the volatility skews) has yielded a general approach: link implied volatilities to strikes. Now, some brokers provide 3-D matrix as an indicative volatility level for different strikes.

This note describes how to utilize broker’s information to get strike-adjusted implied volatility for general terms. The implied volatility surface is important for valuing derivatives.

This is a matrix of at-the-money swaption volatilities with rows at different option terms, and columns of swap tenors. Table 1 shows one example of this matrix. Notice that the first tenor is 3-month, which represents 3-month forward volatility.
 




	3 M	1 Y	2 Y	3 Y	4 Y	5 Y	7 Y	10 Y	12 Y	15 Y	20 Y	25 Y	30 Y
1 M	21.00	40.70	36.10	31.80	29.00	27.10	25.00	23.40	22.20	21.00	19.80	18.70	17.60
3 M	21.00	40.70	36.10	32.00	29.30	27.40	25.30	23.70	22.50	21.30	20.10	19.00	17.90
6 M	35.00	37.70	33.00	29.30	27.30	26.00	24.40	23.10	21.90	20.70	19.50	18.40	17.30
9 M	36.00	34.85	30.35	27.40	25.68	24.58	23.28	22.18	20.98	19.83	18.73	17.68	16.63
12 M	34.00	32.00	27.70	25.50	24.05	23.15	22.15	21.25	20.05	18.95	17.95	16.95	15.95
15 M	32.00	30.00	26.53	24.68	23.45	22.65	21.73	20.88	19.70	18.60	17.58	16.58	15.58
18 M	29.00	28.00	25.35	23.85	22.85	22.15	21.30	20.50	19.35	18.25	17.20	16.20	15.20
2 Y	27.00	24.00	23.00	22.20	21.65	21.15	20.45	19.75	18.65	17.55	16.45	15.45	14.45
3 Y	24.00	21.90	21.15	20.70	20.30	19.95	19.30	18.65	17.65	16.55	15.45	14.45	13.45
4 Y	21.00	20.65	20.05	19.65	19.30	18.95	18.30	17.70	16.70	15.60	14.50	13.50	12.50
5 Y	20.00	19.80	19.20	18.80	18.40	18.05	17.40	16.80	15.80	14.70	13.60	12.60	11.60
6 Y	19.00	19.08	18.50	18.13	17.75	17.40	16.75	16.18	15.23	14.23	13.23	12.33	11.43
7 Y	18.00	18.35	17.80	17.45	17.10	16.75	16.10	15.55	14.65	13.75	12.85	12.05	11.25
8 Y	17.50	17.70	17.15	16.80	16.47	16.10	15.48	14.93	14.07	13.20	12.33	11.57	10.80
9 Y	17.50	17.05	16.50	16.15	15.83	15.45	14.87	14.32	13.48	12.65	11.82	11.08	10.35
10 Y	17.50	16.40	15.85	15.50	15.20	14.80	14.25	13.70	12.90	12.10	11.30	10.60	9.90
12 Y	17.00	16.20	15.65	15.30	15.00	14.60	14.05	13.50	12.77	12.00	11.20	10.48	9.80
18 Y	15.00	15.90	15.35	15.00	14.70	14.30	13.75	13.20	12.58	11.85	11.05	10.29	9.65
20 Y	13.00	15.40	14.85	14.50	14.20	13.80	13.25	12.70	12.26	11.60	10.80	9.98	9.40
25 Y	13.00	15.15	14.60	14.25	13.95	13.55	13.00	12.45	12.10	11.48	10.68	9.82	9.28
30 Y	13.00	15.03	14.48	14.13	13.83	13.43	12.88	12.33	12.02	11.41	10.61	9.74	9.21


Table 1. ATM Swaption Vol Matrix


Brokers can provide market traded benchmark skew matrices. One format is similar to Table 2. It is one of many matrices. Each matrix corresponds to one swap tenor. The rows are for option terms, and the columns are for offsets from at-the-money level (in basis points). 





	-300	-250	-200	-150	-100	-50	0	50	100	150	200	250	300
1m	19.85	17.60	14.60	12.85	7.85	2.55	0	-1.30	0.80	0.80	1.00	2.00	3.20
3m	17.85	15.60	12.60	10.85	6.35	2.15	0	-1.20	-1.45	-1.55	0.65
1.25	1.75
6m	15.85	13.60	10.60	8.85	3.65	1.95	0	-1.10	-1.35	-1.85	-1.55	-1.45	-0.85
1y	13.85	11.60	8.60	6.85	3.05	1.75	0	-1.00	-1.25	-1.65	-1.65	-1.45	-1.35
3y	7.45	5.90	4.70	3.75	2.35	1.55	0	-0.70	-1.05	-1.45	-1.45	-1.35	-1.20


Table 2. An example of swaption skew matrix


For example, if ATM 1y-1y swaption vol is 32%, and Table 2 is the level for 1y swaps, then for a 1y-1y swaption with strike at 50bps above the ATM level, the volatility is 31%.

Broker-provided 3D matrices (with dimensions of option term, strike offset, and swap tenor) are only at certain points. We use linear interpolation to get skew adjustment for those whose terms are not exactly at these points. 

Cap volatility skew is relatively simpler than swaption since it has only two dimensions: cap term and strike offset. Bilinear interpolation can be applied to get general term volatility level. Table 3 shows an example of cap skew matrix.



	-150	-100	-50	ATM Vol	50	100	150

1	12.20	10.20	5.70	37.50	-0.50	-0.75	1.00
2	8.65	6.00	3.20	35.00
-0.90	-2.45	-3.10

3	6.25	4.10	2.00	30.90	-0.80	-2.05	-2.70
4
5.20	3.70	1.75	28.45	-0.70	-1.95	-2.50
5	5.00	3.50	1.45	26.50	-0.65	-1.75	-2.35

6	4.85	3.40	1.38	25.60	-0.63	-1.63	-2.13
7	4.70	3.30	1.30	24.70	-0.60	-1.50	-1.90

8	4.53	3.10	1.23	24.07	-0.53	-1.40	-1.77

Table 3. An example of cap skew matrix


For instance, a 2-year cap with strike of 50 basis points lower than the strike should be priced with volatility of 38.20%. For 1-month, 3-month and 6-month forward LIBOR volatility smiles, we use simple approximation as explained below: 

From broker’s quote the current shortest swap tenor is 1-year, and the shortest option term is 1-month. To get forward 3-month LIBOR smiles, we proceed as follows

1.	For 1-month, 3-month and 6-month LIBOR, the ATM vols for these rates are the same as 3-month forward vol from the first column of the ATM swaption vol table.

2.	For LIBOR vol smile calculation, we use “1-year swap volatility”.  For example, to derive 2-year into 6-month vol skews, we proceed as

a.	The ATM forward 6-month forward rate is used to compare the strike level, thus we get strike spread;

b.	The ATM vol for this forward rate is the same as 2-year into 3-month vol;

c.	Then from swaption skew matrix of 1-year tenor, we can get  2-year into 1-year skew level;

d.	Add this skew to ATM vol.


	The above discussion provides an approach to value European style swaption and cap/floor with volatility smiles. In this section, we describe a method to price American/Bermudan swaptions with smiles.

In our model for valuing American/Bermudan style swaptions, the volatility curve is entered as forward swaption vols.  It is therefore necessary to ensure that the volatility curve is smile-adjusted.

Our method is:

1.	At each point, the difference between forward swap rate and the strike is calculated.

2.	Use the method for calculating smile-adjusted volatility for European options mentioned before, we can get the volatility for the point.

3.	 Apply the volatility curve.

This method will correctly reflect the true hedging costs if American/Bermudan swaptions are hedged by European swaptions.

Reference:

https://finpricing.com/curveVolList.html

