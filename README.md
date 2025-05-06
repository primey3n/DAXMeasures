TempxInput = 
VAR SelectedDate = SELECTEDVALUE(Dim_CalendarTbl[Date])
VAR CurrentHour = 
LOOKUPVALUE(Dim_HourTbl[Order],'Dim_HourTbl'[Hour],format(NOW(),"H"))
VAR RunningInput =
CALCULATE(
    SUM(Fact_REPIInput[QTY])/8 * CurrentHour/1000,
    FILTER(
        CALCULATETABLE(
            SUMMARIZE(Dim_HourTbl,Dim_HourTbl[Order],Dim_HourTbl[HourLbl]),
            ALLSELECTED('Fact_REPIInput')
        ),
        ISONORAFTER(
            'Dim_HourTbl'[Order], MAX('Dim_HourTbl'[Order]),DESC)
    
))
RETURN
if (SelectedDate < TODAY(),8,CurrentHour)
