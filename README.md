RunningInput3H = 
VAR hournow = HOUR(NOW())
VAR dateselected = MAX(Dim_CalendarTbl[Date])
VAR lookupvar = LOOKUPVALUE(Dim_HourTbl[Order], Dim_HourTbl[Hour], hournow)

// Determine multiplier based on date and hour conditions
VAR Multiplier =
    SWITCH(
        TRUE(),
        dateselected = TODAY() && hournow > 18 && hournow < 24, 8,
        dateselected > TODAY() && hournow >= 18 && hournow < 24,lookupvar,
        dateselected > TODAY() && hournow >= 0,lookupvar,8
    )

// Calculate running input based on multiplier and filtered hours
VAR RunningInput =
    CALCULATE(
        SUM(Fact_REPIInput[QTY]) / 8 * Multiplier / 1000,
        FILTER(
            CALCULATETABLE(
                SUMMARIZE(Dim_HourTbl, Dim_HourTbl[Order], Dim_HourTbl[HourLbl]),
                ALLSELECTED(Fact_REPIInput)
            ),
            ISONORAFTER(Dim_HourTbl[Order], Multiplier, DESC)
        )
    )

RETURN
    RunningInput







tempx = 
VAR SelectedDate = SELECTEDVALUE(Dim_CalendarTbl[Date])
VAR CurrentHour = if(SelectedDate <= TODAY(),8,LOOKUPVALUE(Dim_HourTbl[Order],'Dim_HourTbl'[Hour],format(NOW(),"H")*1))
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
RunningInput
