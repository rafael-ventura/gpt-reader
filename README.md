chat baseado no meu arquivo de configuracao, monte o sql server script para criar essa tabela.

using System;
using JJ.IP.IPS.Api.Application.v1.Contract.Enums;
using JJ.IP.IPS.Api.Debt.Domain.Models;
using Microsoft.EntityFrameworkCore;
using Microsoft.EntityFrameworkCore.Metadata.Builders;

namespace JJ.IP.IPS.Api.Infra.Data.Configurations;

internal class OpCreditTransferConfiguration : IEntityTypeConfiguration<CtDebt>
{
    public void Configure(EntityTypeBuilder<CtDebt> creditTransfer)
    {
        creditTransfer.ToTable("TbJjIpIpsApi_OpCreditTransfer",
            cfg => cfg.HasComment("Call to the Debit Payment Order API (Payer)."));

        creditTransfer.HasKey(e => e.IdReqOpDebitoJdPi)
            .HasName("PkJjIpIpsApi_OpCreditTransfer")
            .IsClustered(false);

        creditTransfer.Property(e => e.IdReqOpDebitoJdPi)
            .HasComment("Identificador da requisição gerado pelo JJIP.")
            .ValueGeneratedNever();

        creditTransfer.Property(e => e.ChaveIdempotenciaSc)
            .IsRequired()
            .HasMaxLength(36)
            .IsUnicode(false)
            .HasComment("Chave de idempotência do Sistema Cliente.");

        creditTransfer.Property(e => e.DtContabil)
            .HasComment("Data contábil da 'Credit Transfer'");

        creditTransfer.Property(e => e.DtHrLiquidacao)
            .HasComment("Data hora de liquidação da 'Credit Transfer'");

        creditTransfer.Property(e => e.DtHrRequisicao)
            .HasComment("Data hora da requisição.");

        creditTransfer.Property(e => e.IdEndToEnd)
            .HasMaxLength(32)
            .IsUnicode(false)
            .HasComment("Identificador único da transação de 'Credit Transfer', gerado pelo PSP Debtor.");

        creditTransfer.Property(e => e.IdReqSc)
            .IsRequired()
            .HasMaxLength(36)
            .IsUnicode(false)
            .HasComment("Identificador da requisição gerado pelo Sistema Cliente.");


        // Mapeia os campos com tipos complexos
        creditTransfer.OwnsOne(e => e.RequestDto, complexFields =>
        {
            complexFields.ToJson();

            complexFields.Property(e => e.IdReqSistemaCliente)
                .IsRequired()
                .HasMaxLength(36);

            complexFields.Property(e => e.SettlementMethod)
                .IsRequired();

            complexFields.Property(e => e.InstructionIdentification)
                .IsRequired()
                .HasMaxLength(35);

            complexFields.Property(e => e.EndToEndIdentification)
                .IsRequired()
                .HasMaxLength(35);

            complexFields.Property(e => e.Uetr);

            complexFields.Property(e => e.ServiceLevelCode)
                .HasMaxLength(4);

            complexFields.Property(e => e.ServiceLevelProprietary)
                .HasMaxLength(35);

            complexFields.Property(e => e.LocalInstrument)
                .HasMaxLength(35);

            complexFields.Property(e => e.CategoryPurpose)
                .HasMaxLength(4);

            complexFields.Property(e => e.InterbankSettlementAmount)
                .HasMaxLength(35);

            complexFields.Property(e => e.InterbankSettlementDate);

            complexFields.Property(e => e.ChargeBearer)
                .HasMaxLength(4);

            complexFields.Property(e => e.MemberIdentification)
                .HasMaxLength(9);

            complexFields.OwnsOne(x => x.UltimateDebtor, ultimateDebtor =>
            {
                ultimateDebtor.Property(x => x.Name)
                    .HasMaxLength(140)
                    .IsRequired();

                ultimateDebtor.OwnsOne(x => x.PostalAddress, postalAdress =>
                    {
                        postalAdress.Property(x => x.Department)
                            .HasMaxLength(70);

                        postalAdress.Property(x => x.SubDepartment)
                            .HasMaxLength(70);

                        postalAdress.Property(x => x.StreetName)
                            .HasMaxLength(70);

                        postalAdress.Property(x => x.BuildingNumber)
                            .HasMaxLength(16);

                        postalAdress.Property(x => x.Floor)
                            .HasMaxLength(70);

                        postalAdress.Property(x => x.PostBox)
                            .HasMaxLength(16);

                        postalAdress.Property(x => x.Room)
                            .HasMaxLength(70);

                        postalAdress.Property(x => x.PostCode)
                            .HasMaxLength(16)
                            .IsRequired();

                        postalAdress.Property(x => x.TownName)
                            .HasMaxLength(35)
                            .IsRequired();

                        postalAdress.Property(x => x.TownLocationName)
                            .HasMaxLength(35);

                        postalAdress.Property(x => x.DistrictName)
                            .HasMaxLength(35);

                        postalAdress.Property(x => x.CountrySubDivision)
                            .HasMaxLength(35)
                            .IsRequired();

                        postalAdress.Property(x => x.Country)
                            .HasMaxLength(2)
                            .IsRequired();

                        postalAdress.Property(x => x.AddressLine)
                            .HasMaxLength(70)
                            .IsRequired();
                    }
                );

                ultimateDebtor.OwnsOne(x => x.Identification, identification =>
                    {
                        identification.OwnsOne(x => x.OrganisationIdentification, organisationIdentification =>
                            {
                                organisationIdentification.Property(x => x.AnyBIC)
                                    .HasMaxLength(11);

                                organisationIdentification.Property(x => x.LEI)
                                    .HasMaxLength(35);

                                organisationIdentification.OwnsOne(x => x.Other, other =>
                                    {
                                        other.Property(x => x.Identification)
                                            .HasMaxLength(35)
                                            .IsRequired();

                                        other.Property(x => x.SchemeNameCode)
                                            .HasMaxLength(4);

                                        other.Property(x => x.SchemeNameProprietary)
                                            .HasMaxLength(35);

                                        other.Property(x => x.Issuer)
                                            .HasMaxLength(35);
                                    }
                                );
                            }
                        );

                        identification.OwnsOne(x => x.PrivateIdentification, privateIdentification =>
                            {
                                privateIdentification.Property(x => x.BirthDate);

                                privateIdentification.Property(x => x.ProvinceOfBirth)
                                    .HasMaxLength(35);

                                privateIdentification.Property(x => x.CityOfBirth)
                                    .HasMaxLength(35)
                                    .IsRequired();

                                privateIdentification.Property(x => x.CountryOfBirth)
                                    .HasMaxLength(35)
                                    .IsRequired();

                                privateIdentification.OwnsOne(x => x.Other, other =>
                                    {
                                        other.Property(x => x.Identification)
                                            .HasMaxLength(35)
                                            .IsRequired();

                                        other.Property(x => x.SchemeNameCode)
                                            .HasMaxLength(4);

                                        other.Property(x => x.SchemeNameProprietary)
                                            .HasMaxLength(35);

                                        other.Property(x => x.Issuer)
                                            .HasMaxLength(35);
                                    }
                                );
                            }
                        );
                    }
                );

                ultimateDebtor.Property(x => x.CountryOfResidence)
                    .HasMaxLength(2);
            });

            complexFields.OwnsOne(x => x.InitiatingParty, initiatingParty =>
            {
                initiatingParty.Property(x => x.Name)
                    .HasMaxLength(140)
                    .IsRequired();

                initiatingParty.OwnsOne(x => x.PostalAddress, postalAdress =>
                    {
                        postalAdress.Property(x => x.Department)
                            .HasMaxLength(70);

                        postalAdress.Property(x => x.SubDepartment)
                            .HasMaxLength(70);

                        postalAdress.Property(x => x.StreetName)
                            .HasMaxLength(70);

                        postalAdress.Property(x => x.BuildingNumber)
                            .HasMaxLength(16);

                        postalAdress.Property(x => x.Floor)
                            .HasMaxLength(70);

                        postalAdress.Property(x => x.PostBox)
                            .HasMaxLength(16);

                        postalAdress.Property(x => x.Room)
                            .HasMaxLength(70);

                        postalAdress.Property(x => x.PostCode)
                            .HasMaxLength(16)
                            .IsRequired();

                        postalAdress.Property(x => x.TownName)
                            .HasMaxLength(35)
                            .IsRequired();

                        postalAdress.Property(x => x.TownLocationName)
                            .HasMaxLength(35);

                        postalAdress.Property(x => x.DistrictName)
                            .HasMaxLength(35);

                        postalAdress.Property(x => x.CountrySubDivision)
                            .HasMaxLength(35)
                            .IsRequired();

                        postalAdress.Property(x => x.Country)
                            .HasMaxLength(2)
                            .IsRequired();

                        postalAdress.Property(x => x.AddressLine)
                            .HasMaxLength(70)
                            .IsRequired();
                    }
                );

                initiatingParty.OwnsOne(x => x.Identification, identification =>
                    {
                        identification.OwnsOne(x => x.OrganisationIdentification, organisationIdentification =>
                            {
                                organisationIdentification.Property(x => x.AnyBIC)
                                    .HasMaxLength(11);

                                organisationIdentification.Property(x => x.LEI)
                                    .HasMaxLength(35);

                                organisationIdentification.OwnsOne(x => x.Other, other =>
                                    {
                                        other.Property(x => x.Identification)
                                            .HasMaxLength(35)
                                            .IsRequired();

                                        other.Property(x => x.SchemeNameCode)
                                            .HasMaxLength(4);

                                        other.Property(x => x.SchemeNameProprietary)
                                            .HasMaxLength(35);

                                        other.Property(x => x.Issuer)
                                            .HasMaxLength(35);
                                    }
                                );
                            }
                        );

                        identification.OwnsOne(x => x.PrivateIdentification, privateIdentification =>
                            {
                                privateIdentification.Property(x => x.BirthDate);

                                privateIdentification.Property(x => x.ProvinceOfBirth)
                                    .HasMaxLength(35);

                                privateIdentification.Property(x => x.CityOfBirth)
                                    .HasMaxLength(35)
                                    .IsRequired();

                                privateIdentification.Property(x => x.CountryOfBirth)
                                    .HasMaxLength(35)
                                    .IsRequired();

                                privateIdentification.OwnsOne(x => x.Other, other =>
                                    {
                                        other.Property(x => x.Identification)
                                            .HasMaxLength(35)
                                            .IsRequired();

                                        other.Property(x => x.SchemeNameCode)
                                            .HasMaxLength(4);

                                        other.Property(x => x.SchemeNameProprietary)
                                            .HasMaxLength(35);

                                        other.Property(x => x.Issuer)
                                            .HasMaxLength(35);
                                    }
                                );
                            }
                        );
                    }
                );

                initiatingParty.Property(x => x.CountryOfResidence)
                    .HasMaxLength(2);
            });

            complexFields.OwnsOne(x => x.Debtor, debtor =>
            {
                debtor.Property(x => x.Name)
                    .HasMaxLength(140)
                    .IsRequired();

                debtor.OwnsOne(x => x.PostalAddress, postalAdress =>
                    {
                        postalAdress.Property(x => x.Department)
                            .HasMaxLength(70);

                        postalAdress.Property(x => x.SubDepartment)
                            .HasMaxLength(70);

                        postalAdress.Property(x => x.StreetName)
                            .HasMaxLength(70);

                        postalAdress.Property(x => x.BuildingNumber)
                            .HasMaxLength(16);

                        postalAdress.Property(x => x.Floor)
                            .HasMaxLength(70);

                        postalAdress.Property(x => x.PostBox)
                            .HasMaxLength(16);

                        postalAdress.Property(x => x.Room)
                            .HasMaxLength(70);

                        postalAdress.Property(x => x.PostCode)
                            .HasMaxLength(16)
                            .IsRequired();

                        postalAdress.Property(x => x.TownName)
                            .HasMaxLength(35)
                            .IsRequired();

                        postalAdress.Property(x => x.TownLocationName)
                            .HasMaxLength(35);

                        postalAdress.Property(x => x.DistrictName)
                            .HasMaxLength(35);

                        postalAdress.Property(x => x.CountrySubDivision)
                            .HasMaxLength(35)
                            .IsRequired();

                        postalAdress.Property(x => x.Country)
                            .HasMaxLength(2)
                            .IsRequired();

                        postalAdress.Property(x => x.AddressLine)
                            .HasMaxLength(70)
                            .IsRequired();
                    }
                );

                debtor.OwnsOne(x => x.Identification, identification =>
                    {
                        identification.OwnsOne(x => x.OrganisationIdentification, organisationIdentification =>
                            {
                                organisationIdentification.Property(x => x.AnyBIC)
                                    .HasMaxLength(11);

                                organisationIdentification.Property(x => x.LEI)
                                    .HasMaxLength(35);

                                organisationIdentification.OwnsOne(x => x.Other, other =>
                                    {
                                        other.Property(x => x.Identification)
                                            .HasMaxLength(35)
                                            .IsRequired();

                                        other.Property(x => x.SchemeNameCode)
                                            .HasMaxLength(4);

                                        other.Property(x => x.SchemeNameProprietary)
                                            .HasMaxLength(35);

                                        other.Property(x => x.Issuer)
                                            .HasMaxLength(35);
                                    }
                                );
                            }
                        );

                        identification.OwnsOne(x => x.PrivateIdentification, privateIdentification =>
                            {
                                privateIdentification.Property(x => x.BirthDate);

                                privateIdentification.Property(x => x.ProvinceOfBirth)
                                    .HasMaxLength(35);

                                privateIdentification.Property(x => x.CityOfBirth)
                                    .HasMaxLength(35)
                                    .IsRequired();

                                privateIdentification.Property(x => x.CountryOfBirth)
                                    .HasMaxLength(35)
                                    .IsRequired();

                                privateIdentification.OwnsOne(x => x.Other, other =>
                                    {
                                        other.Property(x => x.Identification)
                                            .HasMaxLength(35)
                                            .IsRequired();

                                        other.Property(x => x.SchemeNameCode)
                                            .HasMaxLength(4);

                                        other.Property(x => x.SchemeNameProprietary)
                                            .HasMaxLength(35);

                                        other.Property(x => x.Issuer)
                                            .HasMaxLength(35);
                                    }
                                );
                            }
                        );
                    }
                );

                debtor.Property(x => x.CountryOfResidence)
                    .HasMaxLength(2);
            });

            complexFields.OwnsOne((x => x.DebtorAccount), debtorAccount =>
            {
                debtorAccount.OwnsOne(x => x.Identification, identification =>
                {
                    identification.Property(x => x.IBAN)
                        .HasMaxLength(34)
                        .IsRequired();

                    identification.Property(x => x.Identification)
                        .HasMaxLength(34);
                });

                debtorAccount.OwnsOne(x => x.Type, type =>
                {
                    type.Property(x => x.Code)
                        .HasMaxLength(35);

                    type.Property(x => x.Proprietary)
                        .HasMaxLength(35);
                });

                debtorAccount.OwnsOne(x => x.ProxyType, proxyType =>
                {
                    proxyType.Property(x => x.Code)
                        .HasMaxLength(35);

                    proxyType.Property(x => x.Proprietary)
                        .HasMaxLength(35);
                });

                debtorAccount.Property(x => x.ProxyIdentification)
                    .HasMaxLength(256)
                    .IsRequired();

            });

            complexFields.OwnsOne((x => x.DebtorAgent), debtorAgent =>
            {
                debtorAgent.Property(x => x.BICFI)
                    .HasMaxLength(11);

                debtorAgent.Property(x => x.MemberIdentification)
                    .HasMaxLength(9)
                    .IsRequired();

                debtorAgent.Property(x => x.LEI)
                    .HasMaxLength(20);

                debtorAgent.Property(x => x.Name)
                    .HasMaxLength(140)
                    .IsRequired();

                debtorAgent.OwnsOne(x => x.PostalAddress, postalAdress =>
                    {
                        postalAdress.Property(x => x.Department)
                            .HasMaxLength(70);

                        postalAdress.Property(x => x.SubDepartment)
                            .HasMaxLength(70);

                        postalAdress.Property(x => x.StreetName)
                            .HasMaxLength(70);

                        postalAdress.Property(x => x.BuildingNumber)
                            .HasMaxLength(16);

                        postalAdress.Property(x => x.Floor)
                            .HasMaxLength(70);

                        postalAdress.Property(x => x.PostBox)
                            .HasMaxLength(16);

                        postalAdress.Property(x => x.Room)
                            .HasMaxLength(70);

                        postalAdress.Property(x => x.PostCode)
                            .HasMaxLength(16)
                            .IsRequired();

                        postalAdress.Property(x => x.TownName)
                            .HasMaxLength(35)
                            .IsRequired();

                        postalAdress.Property(x => x.TownLocationName)
                            .HasMaxLength(35);

                        postalAdress.Property(x => x.DistrictName)
                            .HasMaxLength(35);

                        postalAdress.Property(x => x.CountrySubDivision)
                            .HasMaxLength(35)
                            .IsRequired();

                        postalAdress.Property(x => x.Country)
                            .HasMaxLength(2)
                            .IsRequired();

                        postalAdress.Property(x => x.AddressLine)
                            .HasMaxLength(70)
                            .IsRequired();
                    }
                );

            });


            complexFields.OwnsOne((x => x.CreditorAgent), creditorAgent =>
            {
                creditorAgent.Property(x => x.BICFI)
                    .HasMaxLength(11);

                creditorAgent.Property(x => x.MemberIdentification)
                    .HasMaxLength(9)
                    .IsRequired();

                creditorAgent.Property(x => x.LEI)
                    .HasMaxLength(20);

                creditorAgent.Property(x => x.Name)
                    .HasMaxLength(140)
                    .IsRequired();

                creditorAgent.OwnsOne(x => x.PostalAddress, postalAdress =>
                    {
                        postalAdress.Property(x => x.Department)
                            .HasMaxLength(70);

                        postalAdress.Property(x => x.SubDepartment)
                            .HasMaxLength(70);

                        postalAdress.Property(x => x.StreetName)
                            .HasMaxLength(70);

                        postalAdress.Property(x => x.BuildingNumber)
                            .HasMaxLength(16);

                        postalAdress.Property(x => x.Floor)
                            .HasMaxLength(70);

                        postalAdress.Property(x => x.PostBox)
                            .HasMaxLength(16);

                        postalAdress.Property(x => x.Room)
                            .HasMaxLength(70);

                        postalAdress.Property(x => x.PostCode)
                            .HasMaxLength(16)
                            .IsRequired();

                        postalAdress.Property(x => x.TownName)
                            .HasMaxLength(35)
                            .IsRequired();

                        postalAdress.Property(x => x.TownLocationName)
                            .HasMaxLength(35);

                        postalAdress.Property(x => x.DistrictName)
                            .HasMaxLength(35);

                        postalAdress.Property(x => x.CountrySubDivision)
                            .HasMaxLength(35)
                            .IsRequired();

                        postalAdress.Property(x => x.Country)
                            .HasMaxLength(2)
                            .IsRequired();

                        postalAdress.Property(x => x.AddressLine)
                            .HasMaxLength(70)
                            .IsRequired();
                    }
                );

            });

            complexFields.OwnsOne(x => x.Creditor, creditor =>
            {
                creditor.Property(x => x.Name)
                    .HasMaxLength(140)
                    .IsRequired();

                creditor.OwnsOne(x => x.PostalAddress, postalAdress =>
                    {
                        postalAdress.Property(x => x.Department)
                            .HasMaxLength(70);

                        postalAdress.Property(x => x.SubDepartment)
                            .HasMaxLength(70);

                        postalAdress.Property(x => x.StreetName)
                            .HasMaxLength(70);

                        postalAdress.Property(x => x.BuildingNumber)
                            .HasMaxLength(16);

                        postalAdress.Property(x => x.Floor)
                            .HasMaxLength(70);

                        postalAdress.Property(x => x.PostBox)
                            .HasMaxLength(16);

                        postalAdress.Property(x => x.Room)
                            .HasMaxLength(70);

                        postalAdress.Property(x => x.PostCode)
                            .HasMaxLength(16)
                            .IsRequired();

                        postalAdress.Property(x => x.TownName)
                            .HasMaxLength(35)
                            .IsRequired();

                        postalAdress.Property(x => x.TownLocationName)
                            .HasMaxLength(35);

                        postalAdress.Property(x => x.DistrictName)
                            .HasMaxLength(35);

                        postalAdress.Property(x => x.CountrySubDivision)
                            .HasMaxLength(35)
                            .IsRequired();

                        postalAdress.Property(x => x.Country)
                            .HasMaxLength(2)
                            .IsRequired();

                        postalAdress.Property(x => x.AddressLine)
                            .HasMaxLength(70)
                            .IsRequired();
                    }
                );

                creditor.OwnsOne(x => x.Identification, identification =>
                    {
                        identification.OwnsOne(x => x.OrganisationIdentification, organisationIdentification =>
                            {
                                organisationIdentification.Property(x => x.AnyBIC)
                                    .HasMaxLength(11);

                                organisationIdentification.Property(x => x.LEI)
                                    .HasMaxLength(35);

                                organisationIdentification.OwnsOne(x => x.Other, other =>
                                    {
                                        other.Property(x => x.Identification)
                                            .HasMaxLength(35)
                                            .IsRequired();

                                        other.Property(x => x.SchemeNameCode)
                                            .HasMaxLength(4);

                                        other.Property(x => x.SchemeNameProprietary)
                                            .HasMaxLength(35);

                                        other.Property(x => x.Issuer)
                                            .HasMaxLength(35);
                                    }
                                );
                            }
                        );

                        identification.OwnsOne(x => x.PrivateIdentification, privateIdentification =>
                            {
                                privateIdentification.Property(x => x.BirthDate);

                                privateIdentification.Property(x => x.ProvinceOfBirth)
                                    .HasMaxLength(35);

                                privateIdentification.Property(x => x.CityOfBirth)
                                    .HasMaxLength(35)
                                    .IsRequired();

                                privateIdentification.Property(x => x.CountryOfBirth)
                                    .HasMaxLength(35)
                                    .IsRequired();

                                privateIdentification.OwnsOne(x => x.Other, other =>
                                    {
                                        other.Property(x => x.Identification)
                                            .HasMaxLength(35)
                                            .IsRequired();

                                        other.Property(x => x.SchemeNameCode)
                                            .HasMaxLength(4);

                                        other.Property(x => x.SchemeNameProprietary)
                                            .HasMaxLength(35);

                                        other.Property(x => x.Issuer)
                                            .HasMaxLength(35);
                                    }
                                );
                            }
                        );
                    }
                );

                creditor.Property(x => x.CountryOfResidence)
                    .HasMaxLength(2);
            });

            complexFields.OwnsOne((x => x.CreditorAccount), creditorAccount =>
            {
                creditorAccount.OwnsOne(x => x.Identification, identification =>
                {
                    identification.Property(x => x.IBAN)
                        .HasMaxLength(34)
                        .IsRequired();

                    identification.Property(x => x.Identification)
                        .HasMaxLength(34);
                });

                creditorAccount.OwnsOne(x => x.Type, type =>
                {
                    type.Property(x => x.Code)
                        .HasMaxLength(35);

                    type.Property(x => x.Proprietary)
                        .HasMaxLength(35);
                });

                creditorAccount.OwnsOne(x => x.ProxyType, proxyType =>
                {
                    proxyType.Property(x => x.Code)
                        .HasMaxLength(35);

                    proxyType.Property(x => x.Proprietary)
                        .HasMaxLength(35);
                });

                creditorAccount.Property(x => x.ProxyIdentification)
                    .HasMaxLength(256)
                    .IsRequired();

            });

            complexFields.OwnsOne(x => x.UltimateCreditor, ultimateCreditor =>
            {
                ultimateCreditor.Property(x => x.Name)
                    .HasMaxLength(140)
                    .IsRequired();

                ultimateCreditor.OwnsOne(x => x.PostalAddress, postalAdress =>
                    {
                        postalAdress.Property(x => x.Department)
                            .HasMaxLength(70);

                        postalAdress.Property(x => x.SubDepartment)
                            .HasMaxLength(70);

                        postalAdress.Property(x => x.StreetName)
                            .HasMaxLength(70);

                        postalAdress.Property(x => x.BuildingNumber)
                            .HasMaxLength(16);

                        postalAdress.Property(x => x.Floor)
                            .HasMaxLength(70);

                        postalAdress.Property(x => x.PostBox)
                            .HasMaxLength(16);

                        postalAdress.Property(x => x.Room)
                            .HasMaxLength(70);

                        postalAdress.Property(x => x.PostCode)
                            .HasMaxLength(16)
                            .IsRequired();

                        postalAdress.Property(x => x.TownName)
                            .HasMaxLength(35)
                            .IsRequired();

                        postalAdress.Property(x => x.TownLocationName)
                            .HasMaxLength(35);

                        postalAdress.Property(x => x.DistrictName)
                            .HasMaxLength(35);

                        postalAdress.Property(x => x.CountrySubDivision)
                            .HasMaxLength(35)
                            .IsRequired();

                        postalAdress.Property(x => x.Country)
                            .HasMaxLength(2)
                            .IsRequired();

                        postalAdress.Property(x => x.AddressLine)
                            .HasMaxLength(70)
                            .IsRequired();
                    }
                );

                ultimateCreditor.OwnsOne(x => x.Identification, identification =>
                    {
                        identification.OwnsOne(x => x.OrganisationIdentification, organisationIdentification =>
                            {
                                organisationIdentification.Property(x => x.AnyBIC)
                                    .HasMaxLength(11);

                                organisationIdentification.Property(x => x.LEI)
                                    .HasMaxLength(35);

                                organisationIdentification.OwnsOne(x => x.Other, other =>
                                    {
                                        other.Property(x => x.Identification)
                                            .HasMaxLength(35)
                                            .IsRequired();

                                        other.Property(x => x.SchemeNameCode)
                                            .HasMaxLength(4);

                                        other.Property(x => x.SchemeNameProprietary)
                                            .HasMaxLength(35);

                                        other.Property(x => x.Issuer)
                                            .HasMaxLength(35);
                                    }
                                );
                            }
                        );

                        identification.OwnsOne(x => x.PrivateIdentification, privateIdentification =>
                            {
                                privateIdentification.Property(x => x.BirthDate);

                                privateIdentification.Property(x => x.ProvinceOfBirth)
                                    .HasMaxLength(35);

                                privateIdentification.Property(x => x.CityOfBirth)
                                    .HasMaxLength(35)
                                    .IsRequired();

                                privateIdentification.Property(x => x.CountryOfBirth)
                                    .HasMaxLength(35)
                                    .IsRequired();

                                privateIdentification.OwnsOne(x => x.Other, other =>
                                    {
                                        other.Property(x => x.Identification)
                                            .HasMaxLength(35)
                                            .IsRequired();

                                        other.Property(x => x.SchemeNameCode)
                                            .HasMaxLength(4);

                                        other.Property(x => x.SchemeNameProprietary)
                                            .HasMaxLength(35);

                                        other.Property(x => x.Issuer)
                                            .HasMaxLength(35);
                                    }
                                );
                            }
                        );
                    }
                );

                ultimateCreditor.Property(x => x.CountryOfResidence)
                    .HasMaxLength(2);
            });

            complexFields.Property(x => x.PurposeCode)
                .HasMaxLength(4);

            complexFields.Property(x => x.PurposeProprietary)
                .HasMaxLength(35);

            complexFields.OwnsOne(x => x.RelatedRemittanceInformation, relatedRemittanceInformation =>
            {
                relatedRemittanceInformation.Property(x => x.Identification)
                    .HasMaxLength(35);

                relatedRemittanceInformation.Property(x => x.Method);

                relatedRemittanceInformation.Property(x => x.EletronicAddress)
                    .HasMaxLength(2048);

                relatedRemittanceInformation.Property(x => x.PostalAddressName)
                    .HasMaxLength(140);

                relatedRemittanceInformation.OwnsOne(x => x.PostalAdress, postalAdress =>
                    {
                        postalAdress.Property(x => x.Department)
                            .HasMaxLength(70);

                        postalAdress.Property(x => x.SubDepartment)
                            .HasMaxLength(70);

                        postalAdress.Property(x => x.StreetName)
                            .HasMaxLength(70);

                        postalAdress.Property(x => x.BuildingNumber)
                            .HasMaxLength(16);

                        postalAdress.Property(x => x.Floor)
                            .HasMaxLength(70);

                        postalAdress.Property(x => x.PostBox)
                            .HasMaxLength(16);

                        postalAdress.Property(x => x.Room)
                            .HasMaxLength(70);

                        postalAdress.Property(x => x.PostCode)
                            .HasMaxLength(16)
                            .IsRequired();

                        postalAdress.Property(x => x.TownName)
                            .HasMaxLength(35)
                            .IsRequired();

                        postalAdress.Property(x => x.TownLocationName)
                            .HasMaxLength(35);

                        postalAdress.Property(x => x.DistrictName)
                            .HasMaxLength(35);

                        postalAdress.Property(x => x.CountrySubDivision)
                            .HasMaxLength(35)
                            .IsRequired();

                        postalAdress.Property(x => x.Country)
                            .HasMaxLength(2)
                            .IsRequired();

                        postalAdress.Property(x => x.AddressLine)
                            .HasMaxLength(70)
                            .IsRequired();
                    }
                );

            });

            complexFields.OwnsOne(x => x.RemittanceInformation, remittanceInformation =>
            {
                remittanceInformation.Property(x => x.Unstructured)
                    .HasMaxLength(140);

                remittanceInformation.OwnsOne(x => x.ReferredDocumentInformation, referredDocumentInformation =>
                {
                    referredDocumentInformation.OwnsOne(x => x.Type, type =>
                    {
                        type.Property(x => x.TypeCode)
                            .HasMaxLength(35);

                        type.Property(x => x.TypeProprietary)
                            .HasMaxLength(35);

                        type.Property(x => x.Issuer)
                            .HasMaxLength(35);
                    });

                    referredDocumentInformation.Property(x => x.Number)
                        .HasMaxLength(35);

                    referredDocumentInformation.Property(x => x.RelatedDate);

                    referredDocumentInformation.OwnsMany(x => x.LineDetails, lineDetails =>
                    {
                        lineDetails.OwnsMany(x => x.Identifications, identifications =>
                        {

                            identifications.OwnsOne(x => x.Type, type =>
                            {
                                type.Property(x => x.TypeCode);

                                type.Property(x => x.TypeProprietary)
                                    .HasMaxLength(35);

                                type.Property(x => x.Issuer)
                                    .HasMaxLength(35);
                            });

                            identifications.Property(x => x.Number)
                                .HasMaxLength(35);

                            identifications.Property(x => x.RelatedDate)
                                .HasMaxLength(35);
                        });

                        lineDetails.Property(x => x.Description)
                            .HasMaxLength(2048);

                        lineDetails.OwnsOne(x => x.Amount, amount =>
                        {
                            amount.Property(x => x.DuePayableAmount);

                            amount.OwnsMany(x => x.DiscountAppliedAmountType, discountAppliedAmountType =>
                            {
                                discountAppliedAmountType.Property(x => x.Code);

                                discountAppliedAmountType.Property(x => x.Proprietary)
                                    .HasMaxLength(35);
                            });

                            amount.Property(x => x.DiscountAppliedAmountAmount);

                            amount.Property(x => x.CreditNoteAmount);

                            amount.Property(x => x.TaxAmountType);

                            amount.Property(x => x.TaxAmountAmount);

                            amount.Property(x => x.AdjustmentAmountAndReasonAmount);

                            amount.Property(x => x.AdjustmentAmountAndReasonCreditDebitIndicator);

                            amount.Property(x => x.AdjustmentAmountAndReasonReason);

                            amount.Property(x => x.AdjustmentAmountAndReasonAdditionalInformation);

                            amount.Property(x => x.RemittedAmount);

                        });
                    });


                });

                remittanceInformation.OwnsOne(x => x.ReferredDocumentAmount, amount =>
                {
                    amount.Property(x => x.DuePayableAmount);

                    amount.OwnsMany(x => x.DiscountAppliedAmountType, discountAppliedAmountType =>
                    {
                        discountAppliedAmountType.Property(x => x.Code);

                        discountAppliedAmountType.Property(x => x.Proprietary)
                            .HasMaxLength(35);
                    });

                    amount.Property(x => x.DiscountAppliedAmountAmount);

                    amount.Property(x => x.CreditNoteAmount);

                    amount.Property(x => x.TaxAmountType);

                    amount.Property(x => x.TaxAmountAmount);

                    amount.Property(x => x.AdjustmentAmountAndReasonAmount);

                    amount.Property(x => x.AdjustmentAmountAndReasonCreditDebitIndicator);

                    amount.Property(x => x.AdjustmentAmountAndReasonReason);

                    amount.Property(x => x.AdjustmentAmountAndReasonAdditionalInformation);

                    amount.Property(x => x.RemittedAmount);

                });

                remittanceInformation.OwnsOne(x => x.CreditorReferenceInformation, creditorReferenceInformation =>
                {
                    creditorReferenceInformation.Property(x => x.Proprietary)
                        .HasMaxLength(35);

                    creditorReferenceInformation.Property(x => x.Issuer)
                        .HasMaxLength(35);

                    creditorReferenceInformation.Property(x => x.Reference)
                        .HasMaxLength(35);
                });

                remittanceInformation.OwnsOne(x => x.Invoicer, invoicer =>
                {
                    invoicer.Property(x => x.Name)
                        .HasMaxLength(140);

                    invoicer.Property(x => x.AddressTypeCode);

                    invoicer.Property(x => x.AddressTypeIdentification);

                    invoicer.Property(x => x.AddressTypeIssuer);

                    invoicer.Property(x => x.AddressTypeSchemeName);

                    invoicer.Property(x => x.AddressTypePostalAddress);

                    invoicer.OwnsOne(x => x.Identification, identification =>
                        {
                            identification.OwnsOne(x => x.OrganisationIdentification, organisationIdentification =>
                                {
                                    organisationIdentification.Property(x => x.AnyBIC)
                                        .HasMaxLength(11);

                                    organisationIdentification.Property(x => x.LEI)
                                        .HasMaxLength(35);

                                    organisationIdentification.OwnsOne(x => x.Other, other =>
                                        {
                                            other.Property(x => x.Identification)
                                                .HasMaxLength(35)
                                                .IsRequired();

                                            other.Property(x => x.SchemeNameCode)
                                                .HasMaxLength(4);

                                            other.Property(x => x.SchemeNameProprietary)
                                                .HasMaxLength(35);

                                            other.Property(x => x.Issuer)
                                                .HasMaxLength(35);
                                        }
                                    );
                                }
                            );

                            identification.OwnsOne(x => x.PrivateIdentification, privateIdentification =>
                                {
                                    privateIdentification.Property(x => x.BirthDate);

                                    privateIdentification.Property(x => x.ProvinceOfBirth)
                                        .HasMaxLength(35);

                                    privateIdentification.Property(x => x.CityOfBirth)
                                        .HasMaxLength(35)
                                        .IsRequired();

                                    privateIdentification.Property(x => x.CountryOfBirth)
                                        .HasMaxLength(35)
                                        .IsRequired();

                                    privateIdentification.OwnsOne(x => x.Other, other =>
                                        {
                                            other.Property(x => x.Identification)
                                                .HasMaxLength(35)
                                                .IsRequired();

                                            other.Property(x => x.SchemeNameCode)
                                                .HasMaxLength(4);

                                            other.Property(x => x.SchemeNameProprietary)
                                                .HasMaxLength(35);

                                            other.Property(x => x.Issuer)
                                                .HasMaxLength(35);
                                        }
                                    );
                                }
                            );
                        }
                    );

                    invoicer.OwnsOne(x => x.ContactDetails, contactDetails =>
                    {
                        contactDetails.Property(x => x.Name)
                            .HasMaxLength(140);

                        contactDetails.Property(x => x.PhoneNumber);

                        contactDetails.Property(x => x.MobileNumber);

                        contactDetails.Property(x => x.FaxNumber);

                        contactDetails.Property(x => x.EmailAddress)
                            .HasMaxLength(2048);

                        contactDetails.Property(x => x.EmailPurpose)
                            .HasMaxLength(35);

                        contactDetails.Property(x => x.JobTitle)
                            .HasMaxLength(35);

                        contactDetails.Property(x => x.Responsability)
                            .HasMaxLength(35);

                        contactDetails.Property(x => x.Department)
                            .HasMaxLength(35);

                        contactDetails.Property(x => x.ChannelType)
                            .HasMaxLength(4)
                            .IsRequired();

                        contactDetails.Property(x => x.Identification)
                            .HasMaxLength(128);

                        contactDetails.Property(x => x.PreferredMethod);
                    });
                });

                remittanceInformation.OwnsOne(x => x.Invoicee, invoicee =>
                {
                    invoicee.Property(x => x.Name)
                        .HasMaxLength(140);

                    invoicee.Property(x => x.AddressTypeCode);

                    invoicee.Property(x => x.AddressTypeIdentification);

                    invoicee.Property(x => x.AddressTypeIssuer);

                    invoicee.Property(x => x.AddressTypeSchemeName);

                    invoicee.Property(x => x.AddressTypePostalAddress);

                    invoicee.OwnsOne(x => x.Identification, identification =>
                        {
                            identification.OwnsOne(x => x.OrganisationIdentification, organisationIdentification =>
                                {
                                    organisationIdentification.Property(x => x.AnyBIC)
                                        .HasMaxLength(11);

                                    organisationIdentification.Property(x => x.LEI)
                                        .HasMaxLength(35);

                                    organisationIdentification.OwnsOne(x => x.Other, other =>
                                        {
                                            other.Property(x => x.Identification)
                                                .HasMaxLength(35)
                                                .IsRequired();

                                            other.Property(x => x.SchemeNameCode)
                                                .HasMaxLength(4);

                                            other.Property(x => x.SchemeNameProprietary)
                                                .HasMaxLength(35);

                                            other.Property(x => x.Issuer)
                                                .HasMaxLength(35);
                                        }
                                    );
                                }
                            );

                            identification.OwnsOne(x => x.PrivateIdentification, privateIdentification =>
                                {
                                    privateIdentification.Property(x => x.BirthDate);

                                    privateIdentification.Property(x => x.ProvinceOfBirth)
                                        .HasMaxLength(35);

                                    privateIdentification.Property(x => x.CityOfBirth)
                                        .HasMaxLength(35)
                                        .IsRequired();

                                    privateIdentification.Property(x => x.CountryOfBirth)
                                        .HasMaxLength(35)
                                        .IsRequired();

                                    privateIdentification.OwnsOne(x => x.Other, other =>
                                        {
                                            other.Property(x => x.Identification)
                                                .HasMaxLength(35)
                                                .IsRequired();

                                            other.Property(x => x.SchemeNameCode)
                                                .HasMaxLength(4);

                                            other.Property(x => x.SchemeNameProprietary)
                                                .HasMaxLength(35);

                                            other.Property(x => x.Issuer)
                                                .HasMaxLength(35);
                                        }
                                    );
                                }
                            );
                        }
                    );

                    invoicee.OwnsOne(x => x.ContactDetails, contactDetails =>
                    {
                        contactDetails.Property(x => x.Name)
                            .HasMaxLength(140);

                        contactDetails.Property(x => x.PhoneNumber);

                        contactDetails.Property(x => x.MobileNumber);

                        contactDetails.Property(x => x.FaxNumber);

                        contactDetails.Property(x => x.EmailAddress)
                            .HasMaxLength(2048);

                        contactDetails.Property(x => x.EmailPurpose)
                            .HasMaxLength(35);

                        contactDetails.Property(x => x.JobTitle)
                            .HasMaxLength(35);

                        contactDetails.Property(x => x.Responsability)
                            .HasMaxLength(35);

                        contactDetails.Property(x => x.Department)
                            .HasMaxLength(35);

                        contactDetails.Property(x => x.ChannelType)
                            .HasMaxLength(4)
                            .IsRequired();

                        contactDetails.Property(x => x.Identification)
                            .HasMaxLength(128);

                        contactDetails.Property(x => x.PreferredMethod);
                    });
                });

                remittanceInformation.OwnsOne(x => x.TaxRemittance, taxRemittance =>
                {

                    taxRemittance.OwnsOne(x => x.CreditorTax, creditorTax =>
                    {
                        creditorTax.Property(x => x.TaxIdentification)
                            .HasMaxLength(35);

                        creditorTax.Property(x => x.RegistrationIdentification)
                            .HasMaxLength(35);

                        creditorTax.Property(x => x.TaxType)
                            .HasMaxLength(35);
                    });

                    taxRemittance.OwnsOne(x => x.DebtorTax, debtorTax =>
                    {
                        debtorTax.Property(x => x.TaxIdentification)
                            .HasMaxLength(35);

                        debtorTax.Property(x => x.RegistrationIdentification)
                            .HasMaxLength(35);

                        debtorTax.Property(x => x.TaxType)
                            .HasMaxLength(35);
                    });

                    taxRemittance.Property(x => x.DebtorAuthorisationTitle)
                        .HasMaxLength(35);

                    taxRemittance.Property(x => x.DebtorAuthorisationName)
                        .HasMaxLength(140);

                    taxRemittance.OwnsOne(x => x.UltimateDebtorTax, ultimateDebtorTax =>
                    {
                        ultimateDebtorTax.Property(x => x.TaxIdentification)
                            .HasMaxLength(35);

                        ultimateDebtorTax.Property(x => x.RegistrationIdentification)
                            .HasMaxLength(35);

                        ultimateDebtorTax.Property(x => x.TaxType)
                            .HasMaxLength(35);
                    });

                    taxRemittance.Property(x => x.UltimateDebtorAuthorisationTitle)
                        .HasMaxLength(35);

                    taxRemittance.Property(x => x.UltimateDebtorAuthorisationName)
                        .HasMaxLength(140);

                    taxRemittance.Property(x => x.AdministrationZone)
                        .HasMaxLength(140);

                    taxRemittance.Property(x => x.ReferenceNumber)
                        .HasMaxLength(140);

                    taxRemittance.Property(x => x.Method)
                        .HasMaxLength(35);

                    taxRemittance.Property(x => x.TotalTaxableBaseAmount);

                    taxRemittance.Property(x => x.TotalTaxAmount);

                    taxRemittance.Property(x => x.Date);

                    taxRemittance.Property(x => x.SequenceNumber);

                    taxRemittance.OwnsOne(x => x.Record, record =>
                    {
                        record.Property(x => x.Type)
                            .HasMaxLength(35);

                        record.Property(x => x.Category)
                            .HasMaxLength(35);

                        record.Property(x => x.CategoryDetails)
                            .HasMaxLength(35);

                        record.Property(x => x.DebtorStatus)
                            .HasMaxLength(35);

                        record.Property(x => x.CertificateIdentification)
                            .HasMaxLength(35);

                        record.Property(x => x.FormsCode)
                            .HasMaxLength(35);

                        record.OwnsOne(x => x.Period, period =>
                        {
                            period.Property(x => x.Year);

                            period.Property(x => x.Type);

                            period.Property(x => x.FromDate);

                            period.Property(x => x.ToDate);
                        });

                        record.Property(x => x.Rate);

                        record.Property(x => x.TaxableBaseAmount);

                        record.Property(x => x.TotalAmount);

                        record.OwnsOne(x => x.DetailsPeriod, detailsPeriod =>
                        {
                            detailsPeriod.Property(x => x.Year);

                            detailsPeriod.Property(x => x.Type);

                            detailsPeriod.Property(x => x.FromDate);

                            detailsPeriod.Property(x => x.ToDate);

                        });

                        record.Property(x => x.DetailsAmount);

                        record.Property(x => x.AdditionalInformation)
                            .HasMaxLength(140);
                    });

                });

                remittanceInformation.OwnsOne(x => x.GarnishmentRemittance, garnishmentRemittance =>
                {
                    garnishmentRemittance.OwnsOne(x => x.Type, type =>
                    {
                        type.Property(x => x.TypeCode);

                        type.Property(x => x.TypeProprietary)
                            .HasMaxLength(35);

                        type.Property(x => x.Issuer)
                            .HasMaxLength(35);


                    });

                    garnishmentRemittance.OwnsOne(x => x.Garnishee, garnishee =>
                    {
                        garnishee.Property(x => x.Name)
                            .HasMaxLength(140);

                        garnishee.Property(x => x.AddressTypeCode);

                        garnishee.Property(x => x.AddressTypeIdentification);

                        garnishee.Property(x => x.AddressTypeIssuer);

                        garnishee.Property(x => x.AddressTypeSchemeName);

                        garnishee.Property(x => x.AddressTypePostalAddress);

                        garnishee.OwnsOne(x => x.Identification, identification =>
                            {
                                identification.OwnsOne(x => x.OrganisationIdentification, organisationIdentification =>
                                    {
                                        organisationIdentification.Property(x => x.AnyBIC)
                                            .HasMaxLength(11);

                                        organisationIdentification.Property(x => x.LEI)
                                            .HasMaxLength(35);

                                        organisationIdentification.OwnsOne(x => x.Other, other =>
                                            {
                                                other.Property(x => x.Identification)
                                                    .HasMaxLength(35)
                                                    .IsRequired();

                                                other.Property(x => x.SchemeNameCode)
                                                    .HasMaxLength(4);

                                                other.Property(x => x.SchemeNameProprietary)
                                                    .HasMaxLength(35);

                                                other.Property(x => x.Issuer)
                                                    .HasMaxLength(35);
                                            }
                                        );
                                    }
                                );

                                identification.OwnsOne(x => x.PrivateIdentification, privateIdentification =>
                                    {
                                        privateIdentification.Property(x => x.BirthDate);

                                        privateIdentification.Property(x => x.ProvinceOfBirth)
                                            .HasMaxLength(35);

                                        privateIdentification.Property(x => x.CityOfBirth)
                                            .HasMaxLength(35)
                                            .IsRequired();

                                        privateIdentification.Property(x => x.CountryOfBirth)
                                            .HasMaxLength(35)
                                            .IsRequired();

                                        privateIdentification.OwnsOne(x => x.Other, other =>
                                            {
                                                other.Property(x => x.Identification)
                                                    .HasMaxLength(35)
                                                    .IsRequired();

                                                other.Property(x => x.SchemeNameCode)
                                                    .HasMaxLength(4);

                                                other.Property(x => x.SchemeNameProprietary)
                                                    .HasMaxLength(35);

                                                other.Property(x => x.Issuer)
                                                    .HasMaxLength(35);
                                            }
                                        );
                                    }
                                );
                            }
                        );

                        garnishee.OwnsOne(x => x.ContactDetails, contactDetails =>
                        {
                            contactDetails.Property(x => x.Name)
                                .HasMaxLength(140);

                            contactDetails.Property(x => x.PhoneNumber);

                            contactDetails.Property(x => x.MobileNumber);

                            contactDetails.Property(x => x.FaxNumber);

                            contactDetails.Property(x => x.EmailAddress)
                                .HasMaxLength(2048);

                            contactDetails.Property(x => x.EmailPurpose)
                                .HasMaxLength(35);

                            contactDetails.Property(x => x.JobTitle)
                                .HasMaxLength(35);

                            contactDetails.Property(x => x.Responsability)
                                .HasMaxLength(35);

                            contactDetails.Property(x => x.Department)
                                .HasMaxLength(35);

                            contactDetails.Property(x => x.ChannelType)
                                .HasMaxLength(4)
                                .IsRequired();

                            contactDetails.Property(x => x.Identification)
                                .HasMaxLength(128);

                            contactDetails.Property(x => x.PreferredMethod);
                        });
                    });

                    garnishmentRemittance.OwnsOne(x => x.GarnishmentAdministrator, garnishmentAdministrator =>
                    {
                        garnishmentAdministrator.Property(x => x.Name)
                            .HasMaxLength(140);

                        garnishmentAdministrator.Property(x => x.AddressTypeCode);

                        garnishmentAdministrator.Property(x => x.AddressTypeIdentification);

                        garnishmentAdministrator.Property(x => x.AddressTypeIssuer);

                        garnishmentAdministrator.Property(x => x.AddressTypeSchemeName);

                        garnishmentAdministrator.Property(x => x.AddressTypePostalAddress);

                        garnishmentAdministrator.OwnsOne(x => x.Identification, identification =>
                            {
                                identification.OwnsOne(x => x.OrganisationIdentification, organisationIdentification =>
                                    {
                                        organisationIdentification.Property(x => x.AnyBIC)
                                            .HasMaxLength(11);

                                        organisationIdentification.Property(x => x.LEI)
                                            .HasMaxLength(35);

                                        organisationIdentification.OwnsOne(x => x.Other, other =>
                                            {
                                                other.Property(x => x.Identification)
                                                    .HasMaxLength(35)
                                                    .IsRequired();

                                                other.Property(x => x.SchemeNameCode)
                                                    .HasMaxLength(4);

                                                other.Property(x => x.SchemeNameProprietary)
                                                    .HasMaxLength(35);

                                                other.Property(x => x.Issuer)
                                                    .HasMaxLength(35);
                                            }
                                        );
                                    }
                                );

                                identification.OwnsOne(x => x.PrivateIdentification, privateIdentification =>
                                    {
                                        privateIdentification.Property(x => x.BirthDate);

                                        privateIdentification.Property(x => x.ProvinceOfBirth)
                                            .HasMaxLength(35);

                                        privateIdentification.Property(x => x.CityOfBirth)
                                            .HasMaxLength(35)
                                            .IsRequired();

                                        privateIdentification.Property(x => x.CountryOfBirth)
                                            .HasMaxLength(35)
                                            .IsRequired();

                                        privateIdentification.OwnsOne(x => x.Other, other =>
                                            {
                                                other.Property(x => x.Identification)
                                                    .HasMaxLength(35)
                                                    .IsRequired();

                                                other.Property(x => x.SchemeNameCode)
                                                    .HasMaxLength(4);

                                                other.Property(x => x.SchemeNameProprietary)
                                                    .HasMaxLength(35);

                                                other.Property(x => x.Issuer)
                                                    .HasMaxLength(35);
                                            }
                                        );
                                    }
                                );
                            }
                        );

                        garnishmentAdministrator.OwnsOne(x => x.ContactDetails, contactDetails =>
                        {
                            contactDetails.Property(x => x.Name)
                                .HasMaxLength(140);

                            contactDetails.Property(x => x.PhoneNumber);

                            contactDetails.Property(x => x.MobileNumber);

                            contactDetails.Property(x => x.FaxNumber);

                            contactDetails.Property(x => x.EmailAddress)
                                .HasMaxLength(2048);

                            contactDetails.Property(x => x.EmailPurpose)
                                .HasMaxLength(35);

                            contactDetails.Property(x => x.JobTitle)
                                .HasMaxLength(35);

                            contactDetails.Property(x => x.Responsability)
                                .HasMaxLength(35);

                            contactDetails.Property(x => x.Department)
                                .HasMaxLength(35);

                            contactDetails.Property(x => x.ChannelType)
                                .HasMaxLength(4)
                                .IsRequired();

                            contactDetails.Property(x => x.Identification)
                                .HasMaxLength(128);

                            contactDetails.Property(x => x.PreferredMethod);
                        });
                    });

                    garnishmentRemittance.Property(x => x.ReferenceNumber)
                        .HasMaxLength(140);

                    garnishmentRemittance.Property(x => x.Date);

                    garnishmentRemittance.Property(x => x.RemittedAmount);

                    garnishmentRemittance.Property(x => x.FamilyMedicalInsuranceIndicator);

                    garnishmentRemittance.Property(x => x.EmployeeTerminationIndicator);

                });

            });
        });
    }
}


Considere os campos Data como Date, os campos com coisas relacionadas a Tax ou Ammount como decimais/float e o resto como tipos complexos ou strings, ok?
