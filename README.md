```
%%%%%%%%%% Quant Macro HW1

%%%Question 1

%%(a)
global sigma
sigma=2;
global beta
beta=0.984;
global theta
theta=0.33;
global delta
delta=0.07;
global A
A=0.595;

%solve for numerical steady state values
syms k l
eqn1 = 1/beta == A*theta*(k)^(theta-1)*(l)^(1-theta)+1-delta;
eqn2 = 2*l==A*(1-theta)*(k)^(theta)*(l)^(-theta);
eqn=[eqn1,eqn2];

global steady
steady = solve(eqn,[k l]);

%%(b)
global k_0
k_0=steady.k*0.75;
global l_0
l_0=(A*(1-theta)/2*(k_0)^(theta))^(1/(1+theta));

%%(c)

%Calculate k_2 and l_2
global k_2
k_2=steady.k*0.85;
global l_2
l_2=(A*(1-theta)/2*(k_2)^(theta))^(1/(1+theta));

%Write a function to evaluate Equation 11 (Euler) in Homework, with k_1 being the
%only input

%Write a function to do the bisection method

tic
bisection(@(k_1)euler_c(k_0,k_1,k_2),k_0,k_2,1e-2)
toc

%%(d)
tic
multinewton_w_tolerance(@(k_1)euler_c(k_0,k_1,k_2),0.81,1e-2)
toc

%%(e)
%set initial guess of the k sequence
k_ini_sequence=zeros(101,1);
for i=1:length(k_ini_sequence)
    k_ini_sequence(i)=(0.25/100*(i-1)+0.75)*steady.k;
end

[optimal_path,ite,ite_path]=extended_path_e(k_ini_sequence,1e-4,1e-4);

%plot time series graph
ts_optimal_k=timeseries(optimal_path);
ts_k_0=timeseries(ite_path(:,1));
ts_k_1=timeseries(ite_path(:,2));
ts_k_5=timeseries(ite_path(:,3));
ts_k_15=timeseries(ite_path(:,4));
ts_k_50=timeseries(ite_path(:,5));

%image1
plot(ts_optimal_k)
hold on
plot(ts_k_0)
hold on
plot(ts_k_1)
hold on
plot(ts_k_5)
hold on
plot(ts_k_15)
hold on
plot(ts_k_50)
hold on
title('Capital Path')
hold on
xlabel('Quarters') 
hold on
ylabel('Capital')
hold on
legend({'Optimal','Iteration 0','Iteration 1','Iteration 5',...
    'Iteration 15','Iteration 50'},'Location','southeast')


%%(f)
%Under the original sigma (2), find out paths for output, employment,
%investment, consumption and real interest rate (marginal product of capital) 
%from t=0 to t=100.
%the output from previous question, optimal_path, is the optimal path for
%capital.

investment_sigma2=diff(optimal_path);
employment_sigma2=((A*(1-theta)/2).*(optimal_path).^(theta)).^(1/(1+theta));
output_sigma2=A.*((optimal_path).^theta).*((employment_sigma2).^(1-theta));
consumption_sigma2=zeros(100,1);
for i=1:length(consumption_sigma2)
    consumption_sigma2(i)=output_sigma2(i)+optimal_path(i)*(1-delta)-optimal_path(i+1);
end

mpk_sigma2=zeros(101,1);
for i=1:length(mpk_sigma2)
    mpk_sigma2(i)=(A.*((optimal_path(i)+0.0001).^theta).*...
        ((employment_sigma2(i)).^(1-theta))-output_sigma2(i))...
        /0.0001;
end


%Redo optimal path with sigma=5
sigma=5;
[optimal_path_sigma5,ite_sigma5,ite_path_sigma5]=extended_path_e(k_ini_sequence,1e-4,1e-4);

investment_sigma5=diff(optimal_path_sigma5);
employment_sigma5=((A*(1-theta)/2).*(optimal_path_sigma5).^(theta)).^(1/(1+theta));
output_sigma5=A.*((optimal_path_sigma5).^theta).*((employment_sigma5).^(1-theta));
consumption_sigma5=zeros(100,1);
for i=1:length(consumption_sigma5)
    consumption_sigma5(i)=output_sigma5(i)+optimal_path_sigma5(i)*(1-delta)-optimal_path_sigma5(i+1);
end

mpk_sigma5=zeros(101,1);
for i=1:length(mpk_sigma5)
    mpk_sigma5(i)=(A.*((optimal_path_sigma5(i)+0.0001).^theta).*...
        ((employment_sigma5(i)).^(1-theta))-output_sigma5(i))...
        /0.0001;
end

%plot time series graph
plot(timeseries(employment_sigma2))
hold on
plot(timeseries(employment_sigma5))
hold on
title('Employment')
hold on
xlabel('Quarters') 
hold on
ylabel('Employment')
hold on
legend({'Sigma=2','Sigma=5'},'Location','southeast')

plot(timeseries(investment_sigma2))
hold on
plot(timeseries(investment_sigma5))
hold on
title('Investment')
hold on
xlabel('Quarters') 
hold on
ylabel('Investment')
hold on
legend({'Sigma=2','Sigma=5'},'Location','southeast')

plot(timeseries(output_sigma2))
hold on
plot(timeseries(output_sigma5))
hold on
title('Output')
hold on
xlabel('Quarters') 
hold on
ylabel('Output')
hold on
legend({'Sigma=2','Sigma=5'},'Location','southeast')

plot(timeseries(consumption_sigma2))
hold on
plot(timeseries(consumption_sigma5))
hold on
title('Consumption')
hold on
xlabel('Quarters') 
hold on
ylabel('Consumption')
hold on
legend({'Sigma=2','Sigma=5'},'Location','southeast')

plot(timeseries(mpk_sigma2))
hold on
plot(timeseries(mpk_sigma5))
hold on
title('Real Interest Rate')
hold on
xlabel('Quarters') 
hold on
ylabel('Marginal Product of Capital')
hold on
legend({'Sigma=2','Sigma=5'},'Location','southeast')


%Question 2

%%(a)
sigma=2;
global A_path
A_path=zeros(101,1);
for i=1:4
    A_path(i)=0.595;
end
for i=5:19
    A_path(i)=A_path(i-1)+0.032-0.078*((i-4)^(-0.5));
end
for i=20:length(A_path)
    A_path(i)=0.595;
end

figure 
plot(timeseries(A_path))
hold on
title('Productivity')
hold on
xlabel('Quarters') 
hold on
ylabel('Productivity')


[q2a_optimal_path,q2a_ite,q2a_ite_path]=extended_path_2a(k_ini_sequence,A_path,1e-4,1e-4);

%Find corresponding labor
q2a_employment_it0=zeros(101,1);
q2a_employment_it1=zeros(101,1);
q2a_employment_it5=zeros(101,1);
q2a_employment_it15=zeros(101,1);
q2a_employment_it50=zeros(101,1);
q2a_employment_optimal=zeros(101,1);

for i=1:101
    q2a_employment_it0(i)=((A_path(i)*(1-theta)/2).*(q2a_ite_path(i,1)).^(theta)).^(1/(1+theta));
    q2a_employment_it1(i)=((A_path(i)*(1-theta)/2).*(q2a_ite_path(i,2)).^(theta)).^(1/(1+theta));
    q2a_employment_it5(i)=((A_path(i)*(1-theta)/2).*(q2a_ite_path(i,3)).^(theta)).^(1/(1+theta));
    q2a_employment_it15(i)=((A_path(i)*(1-theta)/2).*(q2a_ite_path(i,4)).^(theta)).^(1/(1+theta));
    q2a_employment_it50(i)=((A_path(i)*(1-theta)/2).*(q2a_ite_path(i,5)).^(theta)).^(1/(1+theta));
    q2a_employment_optimal(i)=((A_path(i)*(1-theta)/2).*(optimal_path(i)).^(theta)).^(1/(1+theta));
end


%Plot path
q2a_ts_optimal_k=timeseries(q2a_optimal_path);
q2a_ts_k_0=timeseries(q2a_ite_path(:,1));
q2a_ts_k_1=timeseries(q2a_ite_path(:,2));
q2a_ts_k_5=timeseries(q2a_ite_path(:,3));
q2a_ts_k_15=timeseries(q2a_ite_path(:,4));
q2a_ts_k_50=timeseries(q2a_ite_path(:,5));

plot(ts_optimal_k)
hold on
plot(ts_k_0)
hold on
plot(ts_k_1)
hold on
plot(ts_k_5)
hold on
plot(ts_k_15)
hold on
plot(ts_k_50)
hold on
title('Capital Path')
hold on
xlabel('Quarters') 
hold on
ylabel('Capital')
hold on
legend({'Optimal','Iteration 0','Iteration 1','Iteration 5',...
    'Iteration 15','Iteration 50'},'Location','southeast')


plot(timeseries(q2a_employment_optimal))
hold on
plot(timeseries(q2a_employment_it0))
hold on
plot(timeseries(q2a_employment_it1))
hold on
plot(timeseries(q2a_employment_it5))
hold on
plot(timeseries(q2a_employment_it15))
hold on
plot(timeseries(q2a_employment_it50))
hold on
title('Employment Path')
hold on
xlabel('Quarters') 
hold on
ylabel('Employment')
hold on
legend({'Optimal','Iteration 0','Iteration 1','Iteration 5',...
    'Iteration 15','Iteration 50'},'Location','southeast')

%%(b)

%calculate output, investment, consumption and real interest rate

q2a_output=zeros(101,1);
for i=1:101
    q2a_output(i)=A_path(i).*((q2a_optimal_path(i)).^theta).*((q2a_employment_optimal(i)).^(1-theta));
end

q2a_investment=diff(q2a_optimal_path);

q2a_consumption=zeros(100,1);
for i=1:100
    q2a_consumption(i)=q2a_output(i)+q2a_optimal_path(i)*(1-delta)-q2a_optimal_path(i+1);
end

q2a_mpk=zeros(100,1);
for i=1:101
    q2a_mpk(i)=(A_path(i).*((q2a_optimal_path(i)+0.0001).^theta).*...
        ((q2a_employment_optimal(i)).^(1-theta))-q2a_output(i))...
        /0.0001;
end

figure
plot(timeseries(q2a_output(2:21)))
title('Output Path')
xlabel('Quarters') 
ylabel('Output')

figure
plot(timeseries(q2a_employment_optimal(2:21)))
title('Employment Path')
xlabel('Quarters') 
ylabel('Employment')

figure
plot(timeseries(q2a_investment(2:21)))
title('Investment Path')
xlabel('Quarters') 
ylabel('Investment')

figure
plot(timeseries(q2a_consumption(2:21)))
title('Consumption Path')
xlabel('Quarters') 
ylabel('Consumption')

figure
plot(timeseries(q2a_mpk(2:21)))
title('Real Interest Rate')
xlabel('Quarters') 
ylabel('Marginal Product of Capital')

%%(c)

%import data variables: GDPC1 PAYEMS GPDIC1 PCEC96
uiopen('C:\Users\Owner\Downloads\GDPC1.xls',1)
uiopen('C:\Users\Owner\Downloads\PAYEMS.xls',1)
uiopen('C:\Users\Owner\Downloads\GPDIC1.xls',1)
uiopen('C:\Users\Owner\Downloads\PCE.xls',1)

%Set 20 quaters of data from 2007Q3.
GDPC1=GDPC1(1:20,1:2);
GPDIC1=GPDIC1(1:20,1:2);
month_2_quarter=[1:3:60];
PAYEMS=PAYEMS(month_2_quarter,1:2);
PCEC96=PCEC96(month_2_quarter,1:2);

%de-trend
detrend_GDPC1=detrend(GDPC1(1:20,2));
detrend_GPDIC1=detrend(GPDIC1(1:20,2));
detrend_PAYEMS=detrend(PAYEMS(1:20,2));
detrend_PCEC96=detrend(PCEC96(1:20,2));

%percentage deviation from trend
percent_GDPC1=detrend_GDPC1./GDPC1(:,1);
percent_GPDIC1=detrend_GPDIC1./GPDIC1(:,1);
percent_PAYEMS=detrend_PAYEMS./PAYEMS(:,1);
percent_PCEC96=detrend_PCEC96./PCEC96(:,1);

%percentage deviation from steady state values
percent_output_steady=q2a_output(2:21)./output_sigma2(101,1)-1;
percent_employment_steady=q2a_employment_optimal(2:21)./employment_sigma2(101,1)-1;
percent_investment_steady=q2a_investment(2:21)./investment_sigma2(100,1)-1;
percent_consumption_steady=q2a_consumption(2:21)./consumption_sigma2(100,1)-1;

%plot output
figure
plot(percent_output_steady)
hold on
plot(percent_GDPC1)
title('Output')
xlabel('Quarters') 
ylabel('Output')
legend({'Model','Data'},'Location','southeast')

figure
plot(percent_employment_steady)
hold on
plot(percent_PAYEMS)
title('Employment')
xlabel('Quarters') 
ylabel('Output')
legend({'Model','Data'},'Location','southeast')

figure
plot(percent_investment_steady)
hold on
plot(percent_GPDIC1)
title('Investment')
xlabel('Quarters') 
ylabel('Output')
legend({'Model','Data'},'Location','southeast')

figure
plot(percent_consumption_steady)
hold on
plot(percent_PCEC96)
title('Consumption')
xlabel('Quarters') 
ylabel('Output')
legend({'Model','Data'},'Location','southeast')







%%(d)
sigma=5;
[q2d_optimal_path,q2d_ite,q2d_ite_path]=extended_path_2a(k_ini_sequence,A_path,1e-4,1e-4);

q2d_employment_optimal=zeros(101,1);

for i=1:101
q2d_employment_optimal(i)=((A_path(i)*(1-theta)/2).*(q2d_optimal_path(i)).^(theta)).^(1/(1+theta));
end

q2d_output=zeros(101,1);
for i=1:101
    q2d_output(i)=A_path(i).*((q2d_optimal_path(i)).^theta).*((q2d_employment_optimal(i)).^(1-theta));
end

q2d_investment=diff(q2d_optimal_path);

q2d_consumption=zeros(100,1);
for i=1:100
    q2d_consumption(i)=q2d_output(i)+q2d_optimal_path(i)*(1-delta)-q2d_optimal_path(i+1);
end

q2d_mpk=zeros(100,1);
for i=1:100
    q2d_mpk(i)=(A_path(i).*((q2d_optimal_path(i)+0.0001).^theta).*...
        ((q2d_employment_optimal(i)).^(1-theta))-q2d_output(i))...
        /0.0001;
end

%plot time series graph
plot(timeseries(q2a_employment_optimal))
hold on
plot(timeseries(q2d_employment_optimal))
hold on
title('Employment')
hold on
xlabel('Quarters') 
hold on
ylabel('Employment')
hold on
legend({'Sigma=2','Sigma=5'},'Location','southeast')

plot(timeseries(q2a_investment))
hold on
plot(timeseries(q2d_investment))
hold on
title('Investment')
hold on
xlabel('Quarters') 
hold on
ylabel('Investment')
hold on
legend({'Sigma=2','Sigma=5'},'Location','southeast')

plot(timeseries(q2a_output))
hold on
plot(timeseries(q2d_output))
hold on
title('Output')
hold on
xlabel('Quarters') 
hold on
ylabel('Output')
hold on
legend({'Sigma=2','Sigma=5'},'Location','southeast')

plot(timeseries(q2a_consumption))
hold on
plot(timeseries(q2d_consumption))
hold on
title('Consumption')
hold on
xlabel('Quarters') 
hold on
ylabel('Consumption')
hold on
legend({'Sigma=2','Sigma=5'},'Location','southeast')

plot(timeseries(q2a_mpk))
hold on
plot(timeseries(q2d_mpk))
hold on
title('Real Interest Rate')
hold on
xlabel('Quarters') 
hold on
ylabel('Marginal Product of Capital')
hold on
legend({'Sigma=2','Sigma=5'},'Location','southeast')


function root=bisection(f,a,b,tolerance)
error=10;
i=1;
while error>tolerance
    %disp(['iteration',num2str(i)]);
    c=(a+b)/2;
    %disp(['potential solution',num2str(double(c))]);
    error=abs(f(c));
    %disp(['error is',num2str(double(error))]);
    i=i+1;
    if error<tolerance
        %disp(['success with root',num2str(double(c))]);
        root=c;
    elseif i>20
        root=c;
        %disp(['too many iterations, root is',num2str(double(c))]);
        return
    else
        %disp(['failure'])
        if sign(f(c))==sign(f(a))
            a=c;
            %disp([num2str(double(a)),'and',num2str(double(b))])
        else
            b=c;
            %disp([num2str(double(a)),'and',num2str(double(b))])
        end
    end
end


function output=euler_2a(k_0,k_1,k_2,A)
global sigma
global beta
global theta
global delta
%preparation (define some values)
l_0=((A*(1-theta)/2)*(k_0)^(theta))^(1/(1+theta));
l_1=((A*(1-theta)/2)*(k_1)^(theta))^(1/(1+theta));
h_k0_k1=A*(k_0)^(theta)*(l_0)^(1-theta)+k_0*(1-delta)-k_1;
h_k1_k2=A*(k_1)^(theta)*(l_1)^(1-theta)+k_1*(1-delta)-k_2;
numerator=(h_k0_k1-(l_0)^2)^(-sigma);
denominator=beta*(h_k1_k2-(l_1)^2)^(-sigma);

%Function to find root
output=A*theta*((k_0)^(theta-1))*((l_0)^(1-theta))+1-delta-numerator/denominator;
end


function output=euler_c(k_0,k_1,k_2)
global sigma
global beta
global theta
global delta
global A
%preparation (define some values)
l_0=((A*(1-theta)/2)*(k_0)^(theta))^(1/(1+theta));
l_1=((A*(1-theta)/2)*(k_1)^(theta))^(1/(1+theta));
h_k0_k1=A*(k_0)^(theta)*(l_0)^(1-theta)+k_0*(1-delta)-k_1;
h_k1_k2=A*(k_1)^(theta)*(l_1)^(1-theta)+k_1*(1-delta)-k_2;
numerator=(h_k0_k1-(l_0)^2)^(-sigma);
denominator=beta*(h_k1_k2-(l_1)^2)^(-sigma);

%Function to find root
output=A*theta*((k_0)^(theta-1))*((l_0)^(1-theta))+1-delta-numerator/denominator;
end


function [output,deri]=euler_d(k_1)
global sigma
global beta
global theta
global delta
global A
global k_0
global l_0
global k_2
global l_2
output=euler_c(k_1);
h=1e-10;
deri=(euler_c(k_1+h/2)-euler_c(k_1-h/2))/(norm(h));

end

function [optimal_path,ite,ite_path]=extended_path_2a(input_k,A_path,tolerance_overall,tolerance_bisection)
ite=1;
error=100;
new_path=zeros(101,1);
new_path(1)=input_k(1);
new_path(101)=input_k(101);
ite_path=zeros(101,5);
ite_path(:,1)=input_k;
while error>tolerance_overall
    disp(['Error',num2str(double(error))]);
    disp(['Iteration',num2str(double(ite))]);
    for i=2:(length(new_path)-1)
        new_path(i)=bisection(@(k_1)euler_2a(input_k(i-1),k_1,input_k(i+1),A_path(i-1)),input_k(i-1),input_k(i+1),tolerance_bisection);
    end
    
    error=norm(input_k-new_path);
    
    if ite==1
        ite_path(:,2)=new_path;
    elseif ite==5
        ite_path(:,3)=new_path;
    elseif ite==15
        ite_path(:,4)=new_path;
    elseif ite==50
        ite_path(:,5)=new_path;
    end
    
    
    if error<tolerance_overall
        optimal_path=new_path;
    else 
        input_k=new_path;
        ite=ite+1;
        
    end
end

function [optimal_path,ite,ite_path]=extended_path_e(input_k,tolerance_overall,tolerance_bisection)
ite=1;
error=100;
new_path=zeros(101,1);
new_path(1)=input_k(1);
new_path(101)=input_k(101);
ite_path=zeros(101,5);
ite_path(:,1)=input_k;
while error>tolerance_overall
    disp(['Error',num2str(double(error))]);
    disp(['Iteration',num2str(double(ite))]);
    for i=2:(length(new_path)-1)
        new_path(i)=bisection(@(k_1)euler_c(input_k(i-1),k_1,input_k(i+1)),input_k(i-1),input_k(i+1),tolerance_bisection);
    end
    
    error=norm(input_k-new_path);
    
    if ite==1
        ite_path(:,2)=new_path;
    elseif ite==5
        ite_path(:,3)=new_path;
    elseif ite==15
        ite_path(:,4)=new_path;
    elseif ite==50
        ite_path(:,5)=new_path;
    end
    
    
    if error<tolerance_overall
        optimal_path=new_path;
    else 
        input_k=new_path;
        ite=ite+1;
        
    end
end

function x=multinewton_w_tolerance(f,x,tolerance)
%Performs multidimensional Newton’s method for the function defined in f
%starting with x
[y,dy]=f(x);
error=1;
i=1;
while error>tolerance
    disp(['iteration',num2str(i)]);
    s=dy\y;
    x=x-s;
    disp(['potential solution',num2str(double(x))]);
    [y,dy]=f(x);
    error=abs(euler_c(y));
    disp(['error is',num2str(double(error))]);
    i=i+1;
end


```
