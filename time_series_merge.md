# Here's the code for an individual study:

anes_cum <- read.dta13("anes_timeseries_cdf.dta",convert.factors=F)
anes_84 <- read.dta13("NES1984.dta",convert.factors = F)

anes_84$year <- 1984

# Copying respondent i.d. into new variable with same name as cumulative file respondent i.d.
# (This step is actually silly/not needed)

anes_84$VCF0006a <- anes_84$V840004  

# Creating new respondent i.d.'s from year plus time series respodent i.d. and solving digits problem: 

anes_84$VCF0006a <- ifelse(nchar(anes_84$V840004) == 1,paste0(anes_84$year,"000",
  anes_84$V840004), ifelse(nchar(anes_84$V840004) == 2,paste0(anes_84$year,"00",
  anes_84$V840004),ifelse(nchar(anes_84$V840004) == 3,paste0(anes_84$year,0,
  anes_84$V840004),paste0(anes_84$year,anes_84$V840004))))
  

# Merging time series study into cumulative file using respondent number var

class(anes_cum$VCF0006a)
class(anes_84$VCF0006a)
anes_84$VCF0006a <- as.numeric(anes_84$VCF0006a)

anes_cum <- merge(anes_cum,anes_84[,c("VCF0006a","V840463")],by="VCF0006a",all.x = T)
