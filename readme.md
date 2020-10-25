# What Features Affect a Borrower's Loan Rate and Prosper Rating
## by Alf Maglalang


## Dataset

This dataset is provided by Prosper a lending company. It has 113937 records with 81 features. It was last updated on 3/11/2014. The 81 features are the following:

> `ListingKey`, `ListingNumber`, `ListingCreationDate`, `CreditGrade`, `Term`, `LoanStatus`, `ClosedDate`, `BorrowerAPR`, `BorrowerRate`, `LenderYield`, `EstimatedEffectiveYield`, `EstimatedLoss`, `EstimatedReturn`, `ProsperRating (numeric)`, `ProsperRating (Alpha)`, `ProsperScore`, `ListingCategory (numeric)`, `BorrowerState`, `Occupation`, `EmploymentStatus`, `EmploymentStatusDuration`, `IsBorrowerHomeowner`, `CurrentlyInGroup`, `GroupKey`, `DateCreditPulled`, `CreditScoreRangeLower`, `CreditScoreRangeUpper`, `FirstRecordedCreditLine`, `CurrentCreditLines`, `OpenCreditLines`, `TotalCreditLinespast7years`, `OpenRevolvingAccounts`, `OpenRevolvingMonthlyPayment`, `InquiriesLast6Months`, `TotalInquiries`, `CurrentDelinquencies`, `AmountDelinquent`, `DelinquenciesLast7Years`, `PublicRecordsLast10Years`, `PublicRecordsLast12Months`, `RevolvingCreditBalance`, `BankcardUtilization`, `AvailableBankcardCredit`, `TotalTrades`, `TradesNeverDelinquent (percentage)`, `TradesOpenedLast6Months`, `DebtToIncomeRatio`, `IncomeRange`, `IncomeVerifiable`, `StatedMonthlyIncome`, `LoanKey`, `TotalProsperLoans`, `TotalProsperPaymentsBilled`, `OnTimeProsperPayments`, `ProsperPaymentsLessThanOneMonthLate`, `ProsperPaymentsOneMonthPlusLate`, `ProsperPrincipalBorrowed`, `ProsperPrincipalOutstanding`, `ScorexChangeAtTimeOfListing`, `LoanCurrentDaysDelinquent`, `LoanFirstDefaultedCycleNumber`, `LoanMonthsSinceOrigination`, `LoanNumber`, `LoanOriginalAmount`, `LoanOriginationDate`, `LoanOriginationQuarter`, `MemberKey`, `MonthlyLoanPayment`, `LP_CustomerPayments`, `LP_CustomerPrincipalPayments`, `LP_InterestandFees`, `LP_ServiceFees`, `LP_CollectionFees`, `LP_GrossPrincipalLoss`, `LP_NetPrincipalLoss`, `LP_NonPrincipalRecoverypayments`, `PercentFunded`, `Recommendations`, `InvestmentFromFriendsCount`, `InvestmentFromFriendsAmount`, `Investors`

> Please click on the following link to see the definitions of the data's features. [Prosper Data Dictionary to Explain Dataset's Variables](https://www.google.com/url?q=https://docs.google.com/spreadsheet/ccc?key%3D0AllIqIyvWZdadDd5NTlqZ1pBMHlsUjdrOTZHaVBuSlE%26usp%3Dsharing&sa=D&ust=1554486256024000)

For this project I selected to examine the interplay of the following features with their respective definitions from [Prosper's Data Dictionary](https://www.google.com/url?q=https://docs.google.com/spreadsheet/ccc?key%3D0AllIqIyvWZdadDd5NTlqZ1pBMHlsUjdrOTZHaVBuSlE%26usp%3Dsharing&sa=D&ust=1554486256024000)

- `ListingKey`: Unique key for each listing, same value as the 'key' used in the listing object in the API.
- `CreditGrade`: The Credit rating that was assigned at the time the listing went live. Applicable for listings pre-2009 period and will only be populated for those listings.
- `BorrowerRate`: The Borrower's interest rate for this loan. 
- `ProsperRating (numeric)`: The  Prosper Rating assigned at the time the listing was created: 0 - N/A, 1 - HR, 2 - E, 3 - D, 4 - C, 5 - B, 6 - A, 7 - AA.  Applicable for loans originated after July 2009.
- `ProsperScore`: A custom risk score built using historical Prosper data. The score ranges from 1-10, with 10 being the best, or lowest risk score.  Applicable for loans originated after July 2009.
- `EmploymentStatusDuration`: The length in months of the employment status at the time the listing was created.
- `IsBorrowerHomeowner`: A Borrower will be classified as a homowner if they have a mortgage on their credit profile or provide documentation confirming they are a homeowner.
- `CreditScoreRangeUpper`: The upper value representing the range of the borrower's credit score as provided by a consumer credit rating agency. 
- `CurrentCreditLines`: Number of current credit lines at the time the credit profile was pulled.
- `CurrentDelinquencies`: Number of accounts delinquent at the time the credit profile was pulled.
- `AvailableBankcardCredit`: The total available credit via bank card at the time the credit profile was pulled.
- `LoanOriginalAmount`: The origination amount of the loan.

I also added 3 features derived from the original selected features:

- `ProsperScoreRange` -- a column containing a string of "incorrect" `ProsperScore`s of duplicated `ListingKey` IDs
- `ProsperRatingGrade` -- a column combining `CreditGrade` and `ProsperRating`
- `IsPost2009` -- a column that is a form of boolean mask differentiating if a `ProsperRatingGrade` is originally from `CreditGrade` or from `ProsperRating`

## Summary of Findings

Before doing the visualizations, I needed to clean the data first. According to the variable definitions, `ListingKey` should be unique. I discovered several duplicated `ListingKey` IDs. The duplicated listings were in all features identical except for one feature, namely `ProsperScore`. I decided to keep one of the duplicated listing. `ProsperScore` is most correlative with `ProsperRating (numeric)` (renamed to `ProsperRating`). There is an almost linear relationship between them. `ProsperScore`'s distribution almost looks normal; and `ProsperRating` is normal. For these reasons, `ProsperRating` will be used as a basis to correct `ProsperScore`. I also created a new column called `ProsperScoreRange` to preserve the series of "incorrect" `ProsperScore`. This is to facilitate a visual scan of how the "incorrect" `ProsperScore` compare to corrected `ProsperScore`. A future automation could predict better corrections based on other features not investigated in this project.

`CreditGrade` and `ProsperRating` are mutually exclusive features using a similar grading system. `CreditGrade` was used before mid-2009, `ProsperRating` after mid-2009. `ProsperRating` is an ordinal categorical feature. I converted `CreditGrade` accordingly and discovered it too is an ordinal categorical variable and has a normal distribution. I decided to merge the 2 features into one column called `ProsperRatingGrade`. Moreover, I created a new column called `IsPost2009` in order to create a form of boolean mask to differentiate whether a rating in `ProsperRatingGrade` is from pre-mid-2009 (`CreditGrade`) or from post-mid-2009 (`ProsperRating`). The mask feature facilitates multivariate analyses later.

`ProsperRatingGrade` and `BorrowerRate` are highly positively correlated leading me to treat them for analytical purposes as independent of each other. I decided to treat these 2 variables as dependent of all the other selected features, namely `EmploymentStatusDuration`, `EmploymentStatus`, `LoanOriginalAmount`, `CreditScore`, `CurrentDelinquencies`, `IsBorrowerHomeowner`, `CurrentCreditLines`, `AvailableBankcardCredit`.

With regplot combining scatter and line, `ProsperRatingGrade` and `BorrowerRate` show that there is a negative relationship even though the scatter shows that there is a spread of datapoints between them. The trend is that the higher the `ProsperRatingGrade` is, the lower the `BorrowerRate` is. This relationship confirms that the higher the rating is, the better it is.

Ultimately, my findings show that the other features have effects on `ProsperRatingGrade` and `BorrowerRate`.
- Although `EmploymentStatus` did not make it to explanatory slides, it is still interesting to see that the density of listings is marked for an employed status (including "Other" and "Not Available"). The listings are much less dense for the unemployed, part-time and retired. But a bivariate boxplot, shows that the `BorrowerRate` mean and upper quartiles of the "Not Employed" status are so much higher than those of an employed status.

- IsBorrowerHomeowner effects on `ProsperRatingGrade` and `BorrowerRate` were not evident at first since Prosper equally dispensed loans regardless of homeownership. But using a plot that has logistic regression option illustrates that homeownership affects BorrowerRate negatively and ProsperRatingGrade positively. If a borrower owns a home, the more likely that the BorrowerRate is low and the ProsperRatingGrade high.

- Just as in `IsBorrowerHomeowner` wherein the relationships to `ProsperRatingGrade` and `BorrowerRate` are "mirrored", `ProsperRatingGrade`'s and `BorrowerRate`'s relationship to `LoanOriginalAmount`, `CreditScore`, `CurrentDelinquencies`, `CurrentCreditLines`, `AvailableBankcardCredit` are also mirrored -- if a feature has a negative relationship to BorrowerRate, it has a positive relationship to ProsperRatingGrade.

## Key Insights for Presentation

Using the mask column of `IsPost2009`, I was able to get a better insight on how `CreditGrade` (pre-mid-2009) and `ProsperRating` (post-mid-2009) related to other features, esp to `CreditScore`. During exploratory, I used a FacetGrid pipeline to plot a series of regplot each for `CreditGrade` (pre-2009) `ProsperRating` (post-2009).
Examining the relationship between `CreditScore` and `BorrowerRate` as axes on the regplots and the rating system as cols option in the FacetGrid, it is evident that `CreditScore` is more closely tied to `CreditGrade`. However, using 2 sets of FacetGrid with a series of regplots was a little busy and may flout the "Data-to-Ink Ratio" rule.
For compactness, I decided to use boxplots for this multivariate relationship. I used the mask `IsPost2009` used as hue in boxplots and with `ProsperRatingGrade` as x-axis and `CreditScore` as y-axis. This graph illustrated the same discovery I had with the complex FacetGrid, i.e. `CreditScore` is more closely tied to `CreditGrade`, i.e. ratings pre-mid-2009.

From bivariate analyses during exploration, I noted the negative relationship between `BorrowerRate` and `ProsperRatingGrade`. Using `IsPost2009` as hue again, I also did a similar boxplot but with `BorrowerRate` in the y-axis. It not only illustrated the negative relationship between `BorrowerRate` and `ProsperRatingGrade`, it also showed
succinctly that after mid-2009 the ratings (namely ProsperRating) are more determinant of `BorrowerRate`, as opposed to pre-mid-2009 where `BorrowerRate` is more varied across ratings.

## References
- https://seaborn.pydata.org/
- https://matplotlib.org/3.1.1/api/axes_api.html
- https://classroom.udacity.com
- https://pandas.pydata.org/
