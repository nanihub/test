# test import { ComponentFixture, TestBed } from '@angular/core/testing';
import { SetupNotificationsComponent } from './setup-notifications.component';
import { Router } from '@angular/router';
import { Subject } from 'rxjs';
import { SnackBarNotificationService } from 'src/common/sharedmodule/services/snack-bar-notification.service';
import { TranslatePipe } from 'src/common/coreservicesmodule';
import { TruCoreModalComponent } from 'truist/tru-core';
import { of } from 'rxjs';

describe('SetupNotificationsComponent', () => {
  let component: SetupNotificationsComponent;
  let fixture: ComponentFixture<SetupNotificationsComponent>;
  let mockRouter: Partial<Router>;
  let mockSnackBarNotificationService: Partial<SnackBarNotificationService>;
  let mockTranslatePipe: Partial<TranslatePipe>;
  let mockTruCoreModalComponent: Partial<TruCoreModalComponent>;

  beforeEach(async () => {
    mockRouter = {
      navigate: jasmine.createSpy('navigate'),
    };
    mockSnackBarNotificationService = {
      openSnackBar: jasmine.createSpy('openSnackBar'),
    };
    mockTranslatePipe = {};
    mockTruCoreModalComponent = {
      show: jasmine.createSpy('show'),
    };

    await TestBed.configureTestingModule({
      declarations: [SetupNotificationsComponent],
      providers: [
        { provide: Router, useValue: mockRouter },
        { provide: SnackBarNotificationService, useValue: mockSnackBarNotificationService },
        { provide: TranslatePipe, useValue: mockTranslatePipe },
        { provide: TruCoreModalComponent, useValue: mockTruCoreModalComponent },
      ],
    }).compileComponents();
  });

  beforeEach(() => {
    fixture = TestBed.createComponent(SetupNotificationsComponent);
    component = fixture.componentInstance;
    fixture.detectChanges();
  });

  it('should create', () => {
    expect(component).toBeTruthy();
  });

  it('should navigate to "/tools/setupNotification" when redirectToSetupNotification is called', () => {
    component.redirectToSetupNotification();
    expect(mockRouter.navigate).toHaveBeenCalledWith(['/tools/setupNotification']);
  });

  it('should navigate to "/tools/manageNotification" when breadCrumbNav is called', () => {
    component.breadCrumbNav();
    expect(mockRouter.navigate).toHaveBeenCalledWith(['/tools/manageNotification']);
  });

  it('should show success snackbar when onSave is called and validateAccount is false', () => {
    component.validateAccount = of(false);
    component.onSave();
    expect(mockSnackBarNotificationService.openSnackBar).toHaveBeenCalledWith({
      message: 'Your notification has been saved',
      notificationType: 'success',
    }, 'success-snackbar');
  });

  it('should show customGroupLeaveModal when canDeactivate is called and isleave is false', () => {
    component.isleave = false;
    component.canDeactivate();
    expect(mockTruCoreModalComponent.show).toHaveBeenCalled();
  });

  it('should not show customGroupLeaveModal when canDeactivate is called and isleave is true', () => {
    component.isleave = true;
    component.canDeactivate();
    expect(mockTruCoreModalComponent.show).not.toHaveBeenCalled();
  });

  it('should set isAccountEmpty to true when onEmitStep2Contine is called with empty accountDetails', () => {
    const event = { selectedAccount: null };
    component.onEmitStep2Contine(event);
    expect(component.isAccountEmpty).toBeTrue();
  });

  it('should set isNotificationSaveEnabled to true when onEmitStep2Contine is called with non-empty accountDetails', () => {
    const event = { selectedAccount: { accountName: 'Test Account' } };
    component.onEmitStep2Contine(event);
    expect(component.isNotificationSaveEnabled).toBeTrue();
  });

  it('should set isNotificationDetailsEmpty to true when onEmitStep1Contine is called with empty notificationDetails', () => {
    const event = {
      notifyAmountOption: null,
      selectedNotification: null,
      notificationAmount: null,
    };
    component.onEmitStep1Contine(event);
    expect(component.isNotificationDetailsEmpty).toBeTrue();
  });

  it('should set isStep2Enabled to true when onEmitStep1Contine is called with non-empty notificationDetails', () => {
    const event = {
      notifyAmountOption: 'option',
      selectedNotification: 'notification',
      notificationAmount: 10,
    };
    component.onEmitStep1Contine(event);
    expect(component.isStep2Enabled).toBeTrue();
  });

  afterEach(() => {
    fixture.destroy();
  });
});
