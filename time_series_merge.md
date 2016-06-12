# Here's the code for an individual study:

anes_cum <- read.dta13("anes_timeseries_cdf.dta",convert.factors=F) # load cumulative file
anes_84 <- read.dta13("NES1984.dta",convert.factors = F) # load 1984 time series study

anes_84$year <- 1984 # create year i.d. variable in time series study

# Copying respondent i.d. into new variable with same name as cumulative file respondent i.d.
# (This step is actually silly/not needed)

anes_84$VCF0006a <- anes_84$V840004  

# Creating new respondent i.d.s from year plus time series respondent i.d., and solving digits problem: 

anes_84$VCF0006a <- ifelse(nchar(anes_84$V840004) == 1,paste0(anes_84$year,"000",
  anes_84$V840004), ifelse(nchar(anes_84$V840004) == 2,paste0(anes_84$year,"00",
  anes_84$V840004),ifelse(nchar(anes_84$V840004) == 3,paste0(anes_84$year,0,
  anes_84$V840004),paste0(anes_84$year,anes_84$V840004))))
  

# Merging time series study into cumulative file using respondent number var

class(anes_cum$VCF0006a)

class(anes_84$VCF0006a)

anes_84$VCF0006a <- as.numeric(anes_84$VCF0006a)

anes_cum <- merge(anes_cum,anes_84[,c("VCF0006a","V840463")],by="VCF0006a",all.x = T)

# Janky attempt at doing this in a loop:

# CODING MULTIPLE MERGES

x <- c(1980,1984,1988)
y <- c("V800516","V840463","V880466") # time series respodent i.d. variables

for (i in x) {
  
  anes <- as.data.frame(read.dta13(paste0("NES",i,".dta"),convert.factors = F)) #
  year <- print(i) #
  anes$year <- year #
  
      for (j in y){
    
        z <- paste0('anes','$',j)
        anes$VCF0006a <- z
        anes$VCF0006a <- ifelse(nchar(anes$VCF0006a) == 1, paste0(year,"000",
          z), ifelse(nchar(anes$VCF0006a) == 2,paste0(year,"00",
          z), ifelse(nchar(anes$VCF0006a) == 3,paste0(year,"0",
          z), paste0(year,z))))  
        anes$VCF0006a <- as.numeric(anes$VCF0006a)
        anes_cum <- merge(anes_cum,anes[,j],
          by="VCF0006a",all.x = T)
        remove(anes)
    
      }
  
}




